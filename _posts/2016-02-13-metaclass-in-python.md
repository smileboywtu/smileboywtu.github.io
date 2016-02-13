---
layout: post
title:  metaclass in python
date:   2016-02-13 07:58:00 +0800
categories: articles
---

*a metaclass is the class of the class. like a class define how an instance
behaves, a metaclass defines how a class behaves. a class is an instance of
a metaclass.*

what many people do not realize, though, is that quite literally **everything**
in python language is an object.

{% highlight python %}

print(type(1))

class MyClass(int):
    def __add__(self, other):
        print("my style of int class.")
        return super(MyClass, self).__add__(other)

one = MyClass(2)

print(one + 3)

{% endhighlight %}

here you can see that a object, which is an instance of MyClass can add to an
integer. we see int really is an object that can be subclassed and extended
just like user-defined classes.

we all know in oop language, the type of objects are instances of classes. so
what's the type of the class?

{% highlight python %}

class Owner(object):
    pass

print(type(Owner))

type

{% endhighlight %}

what this shows in python, classes are objects, and they are objects of type type.
type is the a metaclass: a class which instantiates classes. all new-style
classes in python are instances of the type metaclass, including type itself.

{% highlight python %}

print(type(type))

type

{% endhighlight %}

all class definition process is the process of instantiating type class.
we can learn this from an example:

{% highlight python %}

# type(name, bases, dict)
Owner = type('Owner', (int,), {})

one = Owner(2)

print(one + 3)

5

{% endhighlight %}

so we can use the \_\_new\_\_ or \_\_init\_\_ to customer the process of
creating new class. there comes the metaclass in python.

{% highlight python %}

class Meta(type):
    def __init__(cls, name, bases, dict)；
       super(Meta, cls).__init__(name, bases, dict)

class Owner(object):
    __metaclass__ = Meta

{% endhighlight %}

use \_\_init\_\_ or \_\_new\_\_ in metaclass?

**\_\_new\_\_ is called for the creation of a new class, while \_\_init\_\_ is
called after the class is created, to perform additional initialization before
the class is handed to the caller**.

{% highlight python %}
from pprint import pprint

class Tag1: pass
class Tag2: pass
class Tag3:
    def tag3_method(self): pass

class MetaBase(type):
    def __new__(mcl, name, bases, nmspc):
        print('MetaBase.__new__\n')
        return super(MetaBase, mcl).__new__(mcl, name, bases, nmspc)

    def __init__(cls, name, bases, nmspc):
        print('MetaBase.__init__\n')
        super(MetaBase, cls).__init__(name, bases, nmspc)

class MetaNewVSInit(MetaBase):
    def __new__(mcl, name, bases, nmspc):
        # First argument is the metaclass ``MetaNewVSInit``
        print('MetaNewVSInit.__new__')
        for x in (mcl, name, bases, nmspc): pprint(x)
        print('')
        # These all work because the class hasn't been created yet:
        if 'foo' in nmspc: nmspc.pop('foo')
        name += '_x'
        bases += (Tag1,)
        nmspc['baz'] = 42
        return super(MetaNewVSInit, mcl).__new__(mcl, name, bases, nmspc)

    def __init__(cls, name, bases, nmspc):
        # First argument is the class being initialized
        print('MetaNewVSInit.__init__')
        for x in (cls, name, bases, nmspc): pprint(x)
        print('')
        if 'bar' in nmspc: nmspc.pop('bar') # No effect
        name += '_y' # No effect
        bases += (Tag2,) # No effect
        nmspc['pi'] = 3.14159 # No effect
        super(MetaNewVSInit, cls).__init__(name, bases, nmspc)
        # These do work because they operate on the class object:
        cls.__name__ += '_z'
        cls.__bases__ += (Tag3,)
        cls.e = 2.718

class Test(object):
    __metaclass__ = MetaNewVSInit
    def __init__(self):
        print('Test.__init__')
    def foo(self): print('foo still here')
    def bar(self): print('bar still here')

t = Test()
print('class name: ' + Test.__name__)
print('base classes: ', [c.__name__ for c in Test.__bases__])
print([m for m in dir(t) if not m.startswith("__")])
t.bar()
print(t.e)

""" Output:
MetaNewVSInit.__new__
<class '__main__.MetaNewVSInit'>
'Test'
(<type 'object'>,)
{'__init__': <function __init__ at 0x7ecf0>,
 '__metaclass__': <class '__main__.MetaNewVSInit'>,
 '__module__': '__main__',
 'bar': <function bar at 0x7ed70>,
 'foo': <function foo at 0x7ed30>}

MetaBase.__new__

MetaNewVSInit.__init__
<class '__main__.Test_x'>
'Test'
(<type 'object'>,)
{'__init__': <function __init__ at 0x7ecf0>,
 '__metaclass__': <class '__main__.MetaNewVSInit'>,
 '__module__': '__main__',
 'bar': <function bar at 0x7ed70>,
 'baz': 42}

MetaBase.__init__

Test.__init__
class name: Test_x_z
('base classes: ', ['object', 'Tag1', 'Tag3'])
['bar', 'baz', 'e', 'tag3_method']
bar still here
2.718
"""

{% endhighlight %}

The primary difference is that when overriding \_\_new\_\_() you can change
things like the ‘name’, ‘bases’ and ‘namespace’ arguments before you call the
super constructor and it will have an effect, but doing the same thing in
\_\_init\_\_() you won’t get any results from the constructor call.

Note that, since the base-class version of \_\_init\_\_() doesn’t make any
modifications, it makes sense to call it first, then perform any additional
operations. In C++ and Java, the base-class constructor must be called as the
first operation in a derived-class constructor, which makes sense because
derived-class constructions can then build upon base-class foundations.

you should prefer to use \_\_init\_\_ instead of \_\_new\_\_ but for you must
have to use \_\_new\_\_.


reference
=========

1. [metaprogramming](http://python-3-patterns-idioms-test.readthedocs.org/en/latest/Metaprogramming.html)
2. [A primer on python metaclasses](https://jakevdp.github.io/blog/2012/12/01/a-primer-on-python-metaclasses/)
