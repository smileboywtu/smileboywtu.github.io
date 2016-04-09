---
layout: post
title:  use python with c
date:   2016-04-09 13:48:00 +0800
categories: articles
---

The fundamental nature of Cython can be summed up as follows: Cython is Python
with C data types.

Cython is Python: Almost any piece of Python code is also valid Cython code.
(There are a few Limitations, but this approximation will serve for now.) The
Cython compiler will convert it into C code which makes equivalent calls to the
Python/C API.

But Cython is much more than that, because parameters and variables can be
declared to have C data types. Code which manipulates Python values and C values
can be freely intermixed, with conversions occurring automatically wherever possible.
Reference count maintenance and error checking of Python operations is also automatic,
and the full power of Python’s exception handling facilities, including the try-except
and try-finally statements, is available to you – even in the midst of manipulating C data.

## build Cython

+ using setuptools
+ using pyximportx

If your module doesn’t require any extra C libraries or a special build setup,
then you can use the pyximport module by Paul Prescod and Stefan Behnel to load
.pyx files directly on import, without having to write a setup.py file. It is
shipped and installed with Cython and can be used like this:

hello.pyx source file:

{% highlight python %}

print 'hello, world'

{% endhighlight %}

in your python file main.py :

{% highlight pytho %}

import pyximport

pyximport.install()

import hello

{% endhighlight %}

Since Cython 0.11, the pyximport module also has experimental compilation support
for normal Python modules. This allows you to automatically run Cython on every
.pyx and .py module that Python imports, including the standard library and
installed packages. Cython will still fail to compile a lot of Python modules,
in which case the import mechanism will fall back to loading the Python source
modules instead. The .py import mechanism is installed like this:

{% highlight python %}

pyximport.install(pyimport = True)

{% endhighlight %}

if you want to use the C function in your .pyx file, you need to use the setuptools
to compile the code into .so and then import the c function.

another good way is used the c source code and compile the source into the python
module.

c function source file:

{% highlight c %}

#include <stdio.h>
#include "easy.h"

void greeting(){

  printf("this is the first c python module.\n");
}

{% endhighlight %}

c header function file:

{% highlight c %}

#ifndef _EASY_H__
#define _EASY_H__

void greeting();

#endif

{% endhighlight %}


cython module for wrapping the c source file .pyx:

{% highlight python %}

cdef extern from "easy.h":
  cpdef void greeting()

{% endhighlight %}


compile the c source file to .so file then you can import this module from the
python.

+ compile the .pyx into c code
+ compile all .c file into .so file

notice: this work on mac ox, you can compile use the same way but with different
compiler and flags.

{% highlight shell %}

cython -o easy_.c easy.pyx

cc -shared `python2-config --includes --libs --cflags --ldflags` -o easy.so easy.c easy_.c

{% endhighlight %}

after that you will find the .so file inside the current directory.
