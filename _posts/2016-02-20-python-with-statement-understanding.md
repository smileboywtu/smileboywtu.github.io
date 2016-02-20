---
layout: post
title:  python with statement
date:   2016-02-20 11:18:00 +0800
categories: articles
---

> **source**:
>
> [http://effbot.org/zone/python-with-statement.htm](http://effbot.org/zone/python-with-statement.htm)

As most other things in Python, the with statement is actually very simple, once you understand the problem it’s trying to solve. Consider this piece of code:

{% highlight python %}

set things up
try:
    do something
finally:
    tear things down

{% endhighlight %}

Here, “set things up” could be opening a file, or acquiring some sort of external resource, and “tear things down” would then be closing the file, or releasing or removing the resource. The try-finally construct guarantees that the “tear things down” part is always executed, even if the code that does the work doesn’t finish.

If you do this a lot, it would be quite convenient if you could put the “set things up” and “tear things down” code in a library function, to make it easy to reuse. You can of course do something like

{% highlight python %}

def controlled_execution(callback):
    set things up
    try:
        callback(thing)
    finally:
        tear things down

def my_function(thing):
    do something

controlled_execution(my_function)

{% endhighlight %}

But that’s a bit verbose, especially if you need to modify local variables. Another approach is to use a one-shot generator, and use the for-in statement to “wrap” the code:

{% highlight python %}

def controlled_execution():
    set things up
    try:
        yield thing
    finally:
        tear things down

for thing in controlled_execution():
    do something with thing

{% endhighlight %}

But yield isn’t even allowed inside a try-finally in 2.4 and earlier. And while that could be fixed (and it has been fixed in 2.5), it’s still a bit weird to use a loop construct when you know that you only want to execute something once.

So after contemplating a number of alternatives, GvR and the python-dev team finally came up with a generalization of the latter, using an object instead of a generator to control the behaviour of an external piece of code:

{% highlight python %}

class controlled_execution:
    def __enter__(self):
        set things up
        return thing
    def __exit__(self, type, value, traceback):
        tear things down

with controlled_execution() as thing:
     some code

{% endhighlight %}


Now, when the “with” statement is executed, Python evaluates the expression, calls the **\_\_enter\_\_** method on the resulting value (which is called a “context guard”), and assigns whatever **\_\_enter\_\_** returns to the variable given by as. Python will then execute the code body, and no matter what happens in that code, call the guard object’s **\_\_exit\_\_** method.

As an extra bonus, the **\_\_exit\_\_** method can look at the exception, if any, and suppress it or act on it as necessary. To suppress the exception, just return a true value. For example, the following **\_\_exit\_\_** method swallows any TypeError, but lets all other exceptions through:

{% highlight python %}

def __exit__(self, type, value, traceback):
    return isinstance(value, TypeError)

{% endhighlight %}

Happy coding.
