---
layout: post
title:  python yield using with recursive method 
date:   2016-04-16 16:36:00 +0800
categories: articles
---

let's start with a simple example:

{% highlight python %}

def list(start, end, step):
    """create the index iterator"""
    for index in xrange(start, end, step):
        yield

{% endhighlight %}

this example here just return a index generator. this is quiet easy.
but one thing you must keep inside: the function has return a generator 
object, so if you use **indexes = list(1, 3, 1)** the indexes is a object.

{% highlight python %}

def wrapper():
    """return a generator from other func"""
    return list(4, 9, 0.5)

{% endhighlight %}

here we step further, we return a list(4, 9, 0.5) in the wrapper, that's 
mean: indexes = wrapper(), the indexes will be a generator, not a value.

there comes to the most confusing example:

{% highlight python %}

list = [(1, 2, 3), 4, 5, 6, (7, 8), 9]

def find_element(items):
    """find a single digit element"""
    for item in items:
        if isinstance(item, tuple) or isinstance(item, list):
            find_element(item)
        yield item

{% endhighlight %}

the example above do not work as expected.

as we have talked about before, the find_element returned a 
generator object, so when you step into the find_element function,
you actually get inside a generator. so when you create a another 
generator inside it, you need to menually return the element inside 
the generator back.

{% highlight python %}

def find_element(items):
    """find a single digit element"""
    for item in items:
        if isinstance(item, tuple) or isinstance(item, list):
            yield from find_element(item)
        yield item

{% endhighlight %}

the solution above use the idom inside python 3.3, you need to change 
to for-each structure if you use the python 2.7 version.


