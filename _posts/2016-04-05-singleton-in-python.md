---
layout: post
title:  singleton in python
date:   2016-04-05 08:49:00 +0800
categories: articles
---

singleton is a very useful design pattern in other static language like java and
c++, because python has OOP design, so it's not a bad idea to know how to implement
a singleton design pattern in python.

{% highlight java %}

class Demo{

  static instance;

  private Demo(){
  }

  public Demo getInstance(){
    if(!instance){
      instance = new Demo();
    }
    return instance;
  }
}

{% endhighlight %}

main idea:

- hold a static variable
- use a private constructor as default, so the class can't be instantiate by customer
- provide a public interface for user to get instance

in python, because there is no private constructor, so it's not easy to get an enclosing
scope for the class.

if you understand the singleton design pattern it's not very difficult to implement it.
what we really want with a Singleton is to have a single set of state data for all objects.
That is, you could create as many objects as you want and as long as they all refer to the same state information then you achieve the effect of Singleton.

in python you can use \_\_metaclass\_\_ and decorator to do it.

+ \_\_metaclass\_\_
+ decorator

metaclass is the class of class. so it's really useful for creating a class.

{% highlight python %}

class OnlyOne:
    class __OnlyOne:
        def __init__(self, arg):
            self.val = arg
        def __str__(self):
            return repr(self) + self.val
    instance = None
    def __init__(self, arg):
        if not OnlyOne.instance:
            OnlyOne.instance = OnlyOne.__OnlyOne(arg)
        else:
            OnlyOne.instance.val = arg
    def __getattr__(self, name):
        return getattr(self.instance, name)

{% endhighlight %}

you can also use the \_\_new\_\_ to control the create of the new instance:

{% highlight python %}

class Singleton(object):

    def __new__(cls, *args, **kwargs):
        if not hasattr(cls, '_instance'):
            orig = super(Singleton, cls)
            cls._instance = orig.__new__(cls, *args, **kwargs)
        return cls._instance

{% endhighlight %}

according to the idea: *share the same state*
{% highlight python %}

class Borg:
    _shared_state = {}
    def __init__(self):
        self.__dict__ = self._shared_state

class Singleton(Borg):
    def __init__(self, arg):
        Borg.__init__(self)
        self.val = arg
    def __str__(self): return self.val

{% endhighlight %}

use the metaclass to control the creation:
{% highlight python %}

class SingletonMetaClass(type):
    def __init__(cls,name,bases,dict):
        super(SingletonMetaClass,cls)\
          .__init__(name,bases,dict)
        original_new = cls.__new__
        def my_new(cls,*args,**kwds):
            if cls.instance == None:
                cls.instance = \
                  original_new(cls,*args,**kwds)
            return cls.instance
        cls.instance = None
        cls.__new__ = staticmethod(my_new)

class bar(object):
    __metaclass__ = SingletonMetaClass
    def __init__(self,val):
        self.val = val
    def __str__(self):
        return `self` + self.val

{% endhighlight %}

more easy and suggest way is to use the decorator to create the singleton class.

{% highlight python %}

 def singleton(cls):
     instance = {}
     def wrapper(*args, **kwargs):
         if cls not in instance:
             instance[cls] = cls(*args, **kwargs)
         return instance[cls]
     return wrapper

@singleton
class Demo: pass

{% endhighlight %}

the last way use the python Closures, a function defined inside another function is called a nested function. Nested functions can access variables of the enclosing scope.

{% highlight python %}

def outer(msg):

  def inner():
    print msg

  return inner


say_hello = outer('hello')

say_hello()

del outer

say_hello()

{% endhighlight %}

the message was still remembered although we had already finished executing the outer function and
delete them. This technique by which some data ("Hello") gets attached to the code is called closure in Python.

so come back to our singleton pattern using decorator: when the class decorated with singleton,
the class will have an enclosing scope of instance.

**if you want, you can use the module as a singleton and it will just be imported once.**

## reference

[python3 pattern](http://python-3-patterns-idioms-test.readthedocs.org/en/latest/Singleton.html)
[python Closures](http://www.programiz.com/python-programming/closure)
[Decorators in Python](https://www.scaler.com/topics/python/python-decorators/)
