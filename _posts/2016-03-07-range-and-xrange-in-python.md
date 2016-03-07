---
layout: post
title:  range and xrange in python
date:   2016-03-07 09:02:00 +0800
categories: articles
---

> **source**:
>
> [https://www.quora.com/What-is-the-difference-between-range-and-xrange-how-has-this-changed-over-time](https://www.quora.com/What-is-the-difference-between-range-and-xrange-how-has-this-changed-over-time)

In python 2.x range() returns a list and xrange() returns an xrange object, which is kind of like an iterator and generates the numbers on demand.

{% highlight python %}

In [1]: range(5)
Out[1]: [0, 1, 2, 3, 4]

In [2]: xrange(5)
Out[2]: xrange(5)

In [3]: print xrange.__doc__
xrange([start,] stop[, step]) -> xrange object

Like range(), but instead of returning a list, returns an object that
generates the numbers in the range on demand.  For looping, this is
slightly faster than range() and more memory efficient.

{% endhighlight %}

In python 3.x xrange() has been removed and range() now works like xrange() and returns a range object.

{% highlight python %}

In [4]: range(5)
Out[4]: range(0, 5)

In [5]: print (range.__doc__)
range([start,] stop[, step]) -> range object
Returns a virtual sequence of numbers from start to stop by step.

{% endhighlight %}

Happy coding.
