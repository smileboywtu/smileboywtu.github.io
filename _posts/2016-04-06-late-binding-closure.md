---
layout: post
title:  late binding closure
date:   2016-04-06 09:16:00 +0800
categories: articles
---

Late Binding Closures
Another common source of confusion is the way Python binds its variables in
closures (or in the surrounding global scope).

What You Wrote

{% highlight python %}
def create_multipliers():
    return [lambda x : i * x for i in range(5)]
{% endhighlight %}

What You Might Have Expected to Happen

{% highlight python %}
for multiplier in create_multipliers():
    print multiplier(2)
{% endhighlight %}

A list containing five functions that each have their own closed-over i variable
that multiplies their argument, producing:
{% highlight shell %}
0
2
4
6
8
{% endhighlight %}
What Does Happen
{% highlight shell %}
8
8
8
8
8
{% endhighlight %}

Five functions are created; instead all of them just multiply x by 4.

Python’s closures are late binding. This means that the values of variables used
in closures are looked up at the time the inner function is called.

Here, whenever any of the returned functions are called, the value of i is looked
up in the surrounding scope at call time. By then, the loop has completed and i is
left with its final value of 4.

What’s particularly nasty about this gotcha is the seemingly prevalent misinformation
that this has something to do with lambdas in Python. Functions created with a lambda
expression are in no way special, and in fact the same exact behavior is exhibited by
just using an ordinary def:

{% highlight python %}
def create_multipliers():
    multipliers = []

    for i in range(5):
        def multiplier(x):
            return i * x
        multipliers.append(multiplier)

    return multipliers
{% endhighlight %}

What You Should Do Instead
The most general solution is arguably a bit of a hack. Due to Python’s aforementioned
behavior concerning evaluating default arguments to functions (see Mutable Default Arguments),
you can create a closure that binds immediately to its arguments by using a default arg like so:

{% highlight python %}
def create_multipliers():
    return [lambda x, i=i : i * x for i in range(5)]
{% endhighlight %}

Alternatively, you can use the functools.partial function:

{% highlight python %}
from functools import partial
from operator import mul

def create_multipliers():
    return [partial(mul, i) for i in range(5)]

{% endhighlight %}


## reference

[online book](http://docs.python-guide.org/en/latest/writing/gotchas/)
