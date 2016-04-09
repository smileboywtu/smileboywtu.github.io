---
layout: post
title:  exception handling in cython
date:   2016-04-09 18:57:00 +0800
categories: articles
---

If your cdef or cpdef function or method does not declare a return type (as is
  normal in CPython code), then you get exceptions without any extra effort.

If your cdef or cpdef function or method declares a C-style return type, the error
and exception will be handled this way:

- A plain cdef declared function, that does not return a Python object...
  + Has no way of reporting a Python exception to it’s caller.
  + Will only print a warning message and the exception is ignored.

- In order to propagate exceptions like this to it’s caller, you need to declare
an exception value for it.

There are three forms of declaring an exception for a C compiled program.

{% highlight python %} cdef int spam() except -1: pass {% endhighlight %}
- In the example above, if an error occurs inside spam, it will immediately
  return with the value of -1, causing an exception to be propagated to it’s caller.
- Functions declared with an exception value, should explicitly prevent a return of that value.

{% highlight python %} cdef int spam() except? -1: pass {% endhighlight %}
- Used when a -1 may possibly be returned and is not to be considered an error.
- The "?" tells Cython that -1 only indicates a possible error.
- Now, each time -1 is returned, Cython generates a call to PyErr_Occurred to
  verify it is an actual error.

{% highlight python %} cdef int spam() except *: pass {% endhighlight %}
- A call to PyErr_Occurred happens every time the function gets called.
- A need to propagate errors when returning void must use this version.

## reference

[cython doc](http://docs.cython.org/src/reference/language_basics.html#error-and-exception-handling)
