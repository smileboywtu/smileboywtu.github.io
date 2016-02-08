---
layout: post
title:  python decorator
date:   2016-02-08 17:32:00 +0800
categories: articles
---

we first start with python closure and then get an understanding with
python decorator.

{% highlight python %}

def outer():
    x = 1
    def inner():
        print("value of x is:", x)
    return inner

func = outer()

func()

{% endhighlight %}

Everything works according to Python’s scoping rules - x is a local variable
in our function outer. When inner prints x, Python looks for a local variable
to inner and not finding it looks in the enclosing scope which is the function
outer, finding it there.

But what about things from the point of view of variable lifetime? Our variable
x is local to the function outer which means it only exists while the function
outer is running. We aren’t able to call inner till after the return of outer
so according to our model of how Python works, x shouldn’t exist anymore by
the time we call inner and perhaps a runtime error of some kind should occur.

It turns out that, against our expectations, our returned inner function does
work. **Python supports a feature called function closures which means that
inner functions defined in non-global scope remember what their enclosing
namespaces looked like at definition time.** This can be seen by looking at the
*func.\_\_closure\_\_* attribute of our inner function which contains the
variables in the enclosing scopes.

Remember - the function inner is being newly defined each time the function
outer is called. Right now the value of x doesn’t change so each inner function
we get back does the same thing as another inner function - but what if we
tweaked it a little bit?

{% highlight python %}

def outer(x):
    def inner():
        print(x)
    return inner

func1 = outer(1)
func1()

func2 = outer(2)
func2()

{% endhighlight %}

From this example you can see that closures - the fact that functions remember
their enclosing scope - can be used to build custom functions that have,
essentially, a hard coded argument. We aren’t passing the numbers 1 or 2 to our
inner function but are building custom versions of our inner function that
"remembers" what number it should print.

This alone is a powerful technique - you might even think of it as similar to
object oriented techniques in some ways: outer is a constructor for inner with
x acting like a private member variable. And the uses are numerous - if you are
familiar with the key parameter in Python’s sorted function you have probably
written a lambda function to sort a list of lists by the second item instead of
the first. You might now be able to write an itemgetter function that accepts
the index to retrieve and returns a function that could suitably be passed to
the key parameter.

now get start with the python decorator:

**A decorator is just a callable that takes a function as an argument and
returns a replacement function. We’ll start simply and work our way up to
useful decorators.**

{% highlight python %}

def outer(some_func):
    def inner():
        print('before some func')
        result = some_func()
        return result + 1
    return inner

def func1():
    return 1

func2 = outer(func1)

func2()

{% endhighlight %}

Look carefully through our decorator example. We defined a function named outer
that has a single parameter some_func. Inside outer we define an nested function
named inner. The inner function will print a string then call some_func, catching
its return value.

The value of some_func might be different each time outer is called, but whatever
function it is we’ll call it. Finally inner returns the return value of
some_func() + 1 - and we can see that when we call our returned function stored
in decorated. we get the results of the print and also a return value of 2 instead
of the original return value 1 we would expect to get by calling func1.

we could say the func2 is a decorated version of func1. it's func1 plus some thing.

{% highlight python %}

import time


def get_run_time(some_function):

    def wrapper():
        start = time.time()
        some_function()
        end = time.time()
        return "the function has took : " + str((end - start)) + '\n'
    return wrapper

@get_run_time
def run_test():
    for index in range(1000):
        pass

print(run_test())

{% endhighlight %}

this may be helpful to test the run time of a function.

the decorator can be more generic when work with the \*args and \*\*kwargs.

{% highlight python %}

from functools import wraps
from flask import g, request, redirect, url_for


def login_required(f):
    @wraps(f)
    def decorated_function(*args, **kwargs):
        if g.user is None:
            return redirect(url_for('login', next=request.url))
        return f(*args, **kwargs)
    return decorated_function


@app.route('/secret')
@login_required
def secret():
    pass

{% endhighlight %}

this code comes from the flask source code. from here we also can know that the
decorator work from most inner to outer.

happy coding.
