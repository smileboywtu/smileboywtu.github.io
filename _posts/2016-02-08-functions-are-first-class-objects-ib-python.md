---
layout: post
title:  functions are first class objects in python
date:   2016-02-08 17:06:00 +0800
categories: articles
---

This is simply the observation that in Python, functions are objects like
everything else. Ah, function containing variable, you’re not so special.

{% highlight python %}

def func(x):
    pass

print(func.__class__)

print(issubclass(func.__class__, object))

{% endhighlight %}

You may never have thought of your functions as having attributes - but
functions are objects in Python, just like everything else. (If you find
that confusing wait till you hear that classes are objects in Python, just like
everything else!) Perhaps this is making the point in an academic way
- functions are just regular values like any other kind of value in Python.
That means you can pass functions to functions as arguments or return functions
from functions as return values! If you’ve never thought of this sort of thing
consider the following perfectly legal Python:

{% highlight python %}

def add(x, y):
    return x + y

def do_operation(operation, x, y):
    return operation(x, y)

print(do_operation(add, 1, 2))

{% endhighlight %}

function names are just variable labels like any other variable.

You might have seen this sort of behavior before - Python uses functions as
arguments for frequently used operations like customizing the sorted builtin
by providing a function to the key parameter. But what about returning functions
as values? Consider:

{% highlight python %}

def outter():
    def inner():
        print("inner class")
    return inner

func = outter()

func()

{% endhighlight %}

func first just get the function name of the inner class, and then use this
name to invoke the inner class. this just like the function pointer in c
programming language.
