---
layout: post
title:	python 内存池
date:   2016-06-09 19:58:00 +0800
categories: articles
---

## 背景

既然 CPython 的底层用 C 语言实现，那为何不直接使用标准库中的malloc/realloc/free等
函数进行内存管理呢？这是因为，当 Python 应用频繁地创建和销毁一些小的对象，那么底层
就要多次重复调用 malloc 和 free 等函数进行内存分配。这不仅会引入较大的系统开销，而
且还可能产生大量的内存碎片。为了解决这个问题，Python 实现了一个类似内存池的机
制—pymalloc 来满足较小对象（默认512KB以下）的内存请求。

## Python 内存管理结构

简单地说，allocator 预先向系统申请一定数量的内存空间并格式化，每当有满足条件的
内存请求时，allocator 直接从这些格式化的内存中选择一块满足条件的分配给这个需求。
如果预先申请的内存已经耗尽，那么 allocator 会再向系统申请更多的内存并格式化（前
提是不能超过预先设置的内存池最大容量），然后分配内存。当对象被回收时，如果所占内
存之前由 allocator 从内存池分配，那么回收的内存同样被归还给内存池，以供下次内存
请求使用。如果应用的内存需求大于 pymalloc 设置的阈值，那么解释器再将这个请求交给
底层的 C 函数来实现。

综上，Python 的内存管理是分层的，不同的层次使用不同的内存分配机制。我们先来看下
 Cpython 的内存架构图：

<img class="img-responsive" src="{{ site.baseurl }}/downloads/posts/python_memory/memory_obj.jpg" alt="memory object">

由图可见，Python 中的对象管理主要位于 +1 ~ +3 层。从下向上观察，对于大于512KB
（可以设置更改）的对象，使用 +1层中的 Python 原生内存分配器（Python’s raw memory
allocator）进行分配，它的本质也是调用 C 标准库中的malloc/realloc/free等函数；
对于小于等于 512KB 的对象，内存分配主要由 Python 对象分配器（Python’s object
allocator）实施，也就是本文中所要介绍的 pymalloc 机制；对于一些内置类型，如
int/dict/list/string 等，又会有单独的针对这些内置类型的分配器实现。比如管理
int 类型就使用一个简单的 free list，这些分配器都位于 +1层。

因为本文主要介绍位于 +2 层的 Python 对象分配器，所以下文未明确指明时，都使用
allocator 表示 Python 对象分配器。

## 内存数据结构

pymalloc 把“内存池”主要抽象成用3种数据结构表示： arena， pool 和 block。其中一
个 arena 包含若干个pool，每个 pool 又由若干个大小相等的 block 组成。每当应用申
请一个对象，allocator 将一个满足对象大小的 block 从它所在的 pool 上“摘下”分配
给它，并在对应的 pool 中标记该 block 已分配。当对象被销除时，该 block 将归还给
相应的 pool。 3种数据结构的关系如下图所示：

<img class="img-responsive" src="{{ site.baseurl }}/downloads/posts/python_memory/arena.jpg" alt="memory object">

## 参数配置

首先介绍一些源码中定义的宏，他们描述了 pymalloc 中对应数据结构的属性。

{% highlight c %}
#define SMALL_MEMORY_LIMIT  (64*1024*1024)
{% endhighlight %}
表示 allocator 所能向系统申请的最大内存，也就是内存池的最大容量。默认为 64MB。

{% highlight c %}
#define ARENA_SIZE  (256 << 10)
{% endhighlight %}
表示 arena 对象的大小，也即每次 allocator 向系统申请的内存大小，默认为 256KB。

{% highlight c %}
#define POOL_SIZE   SYSTEM_PAGE_SIZE
{% endhighlight %}
表示 pool 对象的大小，这里默认定义为系统的页大小，通常为 4KB。

可以针对以上宏进行自定义，然后重新编译 Python 即可得到自己的 pymalloc 版本。
当然，前提是你知道这样修改后对自己应用的影响。

### block

实现 allocator 每次是将 block 分配给需求用于存储对象。应注意，同一个 pool
中的 block 大小都相同。block 的大小对应关系如下表：

表1

|  请求大小 |  对应分配的大小  | usedpool对应的索引|
|:--------:|:----------------:|:----------------:|
|1-8       |8                 |0                 |
|9-16      |16                |1                 |
|17-24     |24                |2                 |
|25-32     |32                |3                 |
|33-40     |40                |4                 |
|...       |...               |...               |
|497-504   |504               |62                |
|505-512   |512               |63                |
{: class="table table-condensed table-bordered table-hover"}

这个表格的含义为：当请求的对象大小为第一列中所示时，allocator 会分配对应的第
二列中所示大小的 block 给它。同时，每个 block size 对应一个索引，该索引将在
usedpools 数组中使用（下文将介绍）。

对于 block 的定义较为简单，只用一个指针，用来指向 block 在所属 pool 中的所在地址：

{% highlight c %}
#define uint    unsigned int
typedef uchar   block;
{% endhighlight %}

对于 block 管理的理解需要借助 pool 数据结构，所以该部分也放在 pool 管理之后阐述。

### pool

实现 pool 使用一个名为 pool_header 的 struct 对象表示，它的定义如下：

{% highlight c %}
struct pool_header {
    union { block *_padding;
            uint count; } ref;       /* 已分配的 block 数量 */
    block *freeblock;                /* 当前 pool 的空 block 数量 */
    struct pool_header *nextpool     /* 见下文 */
    struct pool_header *prevpool;    /* 见下文 */
    uint arenaindex;                 /* pool 所属 arena 所在 arenas 数组中的索引*/
    uint szidx;                      /* 当前 pool 保存的 block 大小的索引 */
    uint nextoffset;                 /* 见下文 */
    uint maxnextoffset;              /* 见下文 */
};
{% endhighlight %}

每个字段的含义见注释。每个 pool_header 对象存储在 pool 实际内存空间中的起始位置。

### pool管理

allocator 针对每一个 block size 使用一个带头指针的双向链表，来保存已经部分使用
的 pools 集合。再设置一个全局变量 usedpools，它是一个指针数组，保存上述双向链表
的头指针。每个链表在 usedpools 中的索引为该链表对应的 block size 在表1中对应的
索引值（第三列值）与2的结合。比如所存储的 block 大小为 8KB 的已部分使用的 pools，
它们在表1中对应的索引为0，所以该链表的头指针存储在 usedpools[02] 中；同理，存储
block 大小为 16KB 的相应链表的头指针存储在 usedpools[12] 中，依此类推。

任何一个 pool 对应以下3种状态之一：

+ **used**： 它表示该 pool 已经部分使用，即该 pool 中的一部分 block 已经分配
出去存储对象，另一部分则尚未分配。 处在该状态的 pool 都链接在对应 block 大小的
usedpools 链表中。此时 pool_header 对象中的 nextpool 和 prevpool 成员分别指向
链表中的下一个和前一个对象。 当处于该状态的 pool 仅有一个未分配的 block，则该
block 分配出去后，pool 的状态将转换为 full；当该 pool 仅分配出去了一个 block，
则该 block 被回收后，pool 的状态将转换为 empty。两种状态转换都会将当前 pool
从 usedpools 上的对应链表中删除。

+ **full**： 处于该状态的 pool 不在任何一个链表上，此时该对象的 nextpool 和 prevpool
指针将没有意义。当该 pool 上的一个 block 被回收时，重新将该 pool 链接在适当的
usedpools 链表中。

+ **empty**： 当一个 pool 处在 empty 状态时，它被链接在了所属 arena_object
对象的 freepools 成员指向的列表。此时 pool 的 nextpool 指向该 arena 上的下
一个空闲 pool，prevpool 此时无意义。一个 empty 的 pool 并不继承之前的 block
size。也就是说，一个空闲 pool 下一次被分配给的 block 大小不一定要和上一次使用
的相同。

**注意1**：当某次分配指定大小的 block 时，如果 allocator 发现对应block size
的 usedpools 链表为空，说明当前不存在包含该大小的 pool。此时从 usable_arenas
链表中头结点表示 arena_object 对象的成员 free_pools 指向的 pool（详见下面
arena 管理部分）。

### block管理

理解了 pool 的数据结构，再承接上一节来介绍 block 管理。应注意，pymalloc 在
任何一层（arena / pool / block）内存管理都坚持“直到使用才分配”的原则。所以，
一个 pool 在初始化后，只包含2个 block，只有当这2个 block 均已分配，且又有新
的 block 需求时，才从 nextoffset 后初始化一个新的block。freeblock 成员指向
pool 中可以使用的且已经初始化过的 block，而非当前 pool 中所有未使用的内存。
综上，nextoffset 将整个 pool 划分为两个部分，nextoffset 前面的部分（低址）
表示已经初始化过的 block（注：这部分 block 有些已分配，有的尚未分配且链接
在 freeblock 指向的链表中，但这些 block 都至少被分配过一次）；nextoffset
后面的部分（高址）表示尚未初始化的内存，从严格意义上讲，他们还不能称为 block。
只有当 freeblock 指向的链表为空，又有新的 block 请求时，才会从 nextoffset
后面初始化新的 block。当一个 block 被回收时，它被重新链接在 freeblock 指向的
列表。maxnextoffset 表示 nextoffset 的最大合法值，它在 pool 初始化时赋值。
不难理解，当 nextoffset > maxnextoffset 时，表示该 pool 中的所有 block 都至
少被分配出去一次。

### arena 实现与管理

最后介绍 arena 管理。首先给出它的数据结构：

{% highlight c %}
struct arena_object {
    uptr address;                     /* 所关联 arena 实体的地址 */
    block* pool_address;              /* 该 arena 上下一个可分配的 pool 地址 */
    uint nfreepools;                  /* 当前该 arena 中可分配的 pool 数量 */
    uint ntotalpools;                 /* 该 arena 中总共分配的 pool 数量 */
    struct pool_header* freepools;    /* 链接可分配 pool 链表的头指针 */
    struct arena_object* nextarena;   /* 见下文 */
    struct arena_object* prevarena;   /* 见下文 */
};
{% endhighlight %}

成员的含义如注释所述。不同于 pool，arena 的管理分位两部分，在内存中分别对应两种不
同的实体：arena_object 对象和 arena 实体（该定义非官方，笔者为叙述方便指用）。
arena_object 对象用来存储1个 arena 的属性信息，address 成员指向实际用来给对象
分配空间的 arena 实体地址。任一时刻，一个 arena_object 可能和一个 arena 实体关联，
也可能不和任何 arena 实体关联（由 address == 0 唯一标识）。

arena 作为下层（图1中的 +1 层）内存分配函数的调用者，来直接获得系统分配的内存
或将满足条件的空闲内存归还给 OS。

**注意2**：Python 2.5之前的 allocator 只能向 OS 申请内存分配，而不会将满足条件
的空闲内存归还给 OS。考虑以下情形，一个长时间运行的应用，平均内存使用量很低，但
存在某个时刻，该应用有一个内存使用量非常高的峰值。如果按照Python 2.5之前实现，在
达到峰值时刻，解释器会向 OS 申请大量内存用于对象分配（由 allocator 创建 arena 等）。
但当峰值过后，大量内存被闲置，且没有归还给系统，从而造成内存浪费。因此现在的实
现添加了将内存归还给 OS 的功能，由 PyObject_Free() 函数实现。

以下介绍几个和 arena 相关的全局变量：

- **arenas**：存储当前所有已创建的 arena_object 对象的数组；

- **unused_arena_objects**：单链表，链接所有已经创建的但未分配 arena 实体
的 arena_object 对象；

- **usable_arenas**：双链表，链接部分 arena_object 对象。其中的对象所关联
的 arena 实体或者尚未分配任何 pool，或者已经分配部分 pool 用于对象存储；

- **maxarenas**： 当前已经创建的 arena_objects 数量（非 arena 实体数量）；

- **narenas_currently_allocated**： 当前已分配的 arena 实体所占内存容量；

- **# define INITIAL_ARENA_OBJECTS 16**： arenas 数组的初始大小。

**注意3**：一个关联了 pool 已全部分配的 arena 实体的 arena_object 对象不在任何一个链表上。

{% highlight c %}
static struct arena_object* new_arena(void);
{% endhighlight %}

该函数用来分配一个 arena_object 对象并且初始化该对象关联的 arena 实体。当要创建一
个新的 arena 实体时，从链表 unused_arena_objects 取下第一个节点，用于存放相应
arena 实体的属性。

如果该链表为空，使用 realloc() 函数扩展 arenas 指向的数组，并取下第一个用于分配
arena 实体，剩余的 arena_object 全部链接在该链表中，以供下次使用。每次扩展后，
数组的大小为之前的两倍。

为 arena 实体分配内存时，如果 ARENAS_USE_MMAP 宏已设置，则使用匿名内存映
射 mmap() 来实现，否则使用 malloc() 函数创建。

当 allocator 调用 PyObject_Free() 函数将一个 arena 实体所占的内存归还给系统时，
该实体对应 arena_object 的 address 成员将被置0，然后插入到 unused_arena_objects
的头部。

usable_arenas 链表中存放 pool 已经部分分配或者 pool 尚未分配（但实体空间已分配）
的 arena。这些 arena 依据成员 nfreepools 的大小按升序排序。如此可以使得使用程度
较高的 arena 被优先使用，使用程度较低的 arena 则有更大的概率被归还给系统（参见注意1）。

### allocator 使用
{% highlight c %}
void * PyObject_Malloc(size_t nbytes);
{% endhighlight %}

函数分配实际对象，nbytes为对象的大小。分配的逻辑即上述内容。

最终的 arena 结构图如下所示：

<img class="img-responsive" src="{{ site.baseurl }}/downloads/posts/python_memory/pool_distribute.jpg" alt="memory object">

## 总结

内存管理始终是一门编程语言中非常重要的一部分，掌握了内存管理往往能帮助我们更好地理解
对象的生命周期。Python 内存管理的另一个有趣话题是它的“垃圾回收”机制。它以“引用计数”
为主，并借助“标记-清除”机制消除循环引用带来的影响。为了加速对象的创建，Python 又引入
“分代回收”机制，它缓存部分反复创建和销除的对象，而非在它们释放后直接从内存删除它们，
从而加速下次该对象的创建。

## 参考

[nodefe 博客](http://nodefe.com/implement-of-pymalloc-from-source/)
