---
layout: post
title:  python getattr and getatribute
date:   2016-03-09 08:48:00 +0800
categories: articles
---

A key difference between \_\_getattr\_\_ and \_\_getattribute\_\_ is that
\_\_getattr\_\_ is only invoked if the attribute wasn't found the usual ways.
It's good for implementing a fallback for missing attributes, and is probably
the one of two you want.

\_\_getattribute\_\_ is invoked before looking at the actual attributes on the
object, and so can be tricky to implement correctly. You can end up in infinite
recursions very easily.

New-style classes derive from object, old-style classes are those in Python 2.x
with no explicit base class. But the distinction between old-style and new-style
classes is not the important one when choosing between \_\_getattr\_\_ and
\_\_getattribute\_\_.

## usage

this demo fron other people:

{% highlight python %}

class UrlGenerator(object):
    def __init__(self, root_url):
        self.url = root_url

    def __getattr__(self, item):
        if item == 'get' or item == 'post':
            print self.url
        return UrlGenerator('{}/{}'.format(self.url, item))

{% endhighlight %}

test the class:

{% highlight python %}

url_gen = UrlGenerator('http://xxxx')
url_gen.users.show.get

{% endhighlight %}

will print http://xxxx/users/show and the address of the object.

## reference

+ [stackoverflow](http://stackoverflow.com/questions/3278077/difference-between-getattr-vs-getattribute)
+ [stackoverflow](http://stackoverflow.com/questions/4295678/understanding-the-difference-between-getattr-and-getattribute)
