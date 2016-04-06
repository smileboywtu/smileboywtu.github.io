---
layout: post
title:  mutable argument in  python
date:   2016-04-06 09:10:00 +0800
categories: articles
---

Mutable Default Arguments
Seemingly the most common surprise new Python programmers encounter is Python’s
treatment of mutable default arguments in function definitions.

What You Wrote

{% highlight python %}
def append_to(element, to=[]):
    to.append(element)
    return to
{% endhighlight %}

What You Might Have Expected to Happen

{% highlight python %}
my_list = append_to(12)
print my_list

my_other_list = append_to(42)
print my_other_list
{% endhighlight %}

A new list is created each time the function is called if a second argument isn’t
provided, so that the output is:

[12]
[42]

What Does Happen

[12]
[12, 42]

A new list is created once when the function is defined, and the same list is used in each successive call.

Python’s default arguments are evaluated once when the function is defined, not
each time the function is called (like it is in say, Ruby). This means that if
you use a mutable default argument and mutate it, you will and have mutated that
object for all future calls to the function as well.

## reference

[online book](http://docs.python-guide.org/en/latest/writing/gotchas/)
