---
layout: post
title:  python and c integer variable
date:   2016-02-26 20:22:00 +0800
categories: articles
---

> **source**:
>
> [https://www.daniweb.com/programming/software-development/threads/71008/comparing-python-and-c-part-1-integer-variables](https://www.daniweb.com/programming/software-development/threads/71008/comparing-python-and-c-part-1-integer-variables)

When you declare an integer variable in C, the variable is assigned a fixed memory location with enough space to hold the type integer. When you then initialize the variable with a value, the value is put into that space.

Take a look at the C code sample below:

{% highlight c %}

#include <stdio.h>
int main(void)
{
    int a, b, c;
    // once you declare the variable, it is assigned a fixed memory location
    printf("a at %d  b at %d c at %d\n", &a, &b, &c); // // a at 1245060  b at 1245056 c at 1245052

    // initializes a, b and c to integer 3
    a = b = c = 3;

    printf("a = %d  b = %d  c = %d\n", a, b, c);  // a = 3  b = 3  c = 3
    printf("a at %d  b at %d c at %d\n", &a, &b, &c);  // a at 1245060  b at 1245056 c at 1245052

    a = 4;
    printf("a = %d  b = %d  c = %d\n", a, b, c);  // a = 4  b = 3  c = 3
    // 'a' has changed, but it's address has not
    printf("a at %d  b at %d c at %d\n", &a, &b, &c);  // a at 1245060  b at 1245056 c at 1245052
    a++;  // same as a = a + 1;  or  a += 1;
    printf("a = %d\n", a);  // a = 5

    // the time honored way to swap two variables using a temporary variable
    a = 77;
    b = 99;
    printf("before swap: a = %d  b = %d\n", a, b);
    c = b;  // here c is the temporary variable
    b = a;
    a = c;
    printf("after swap:  a = %d  b = %d\n", a, b);
    // exceeding the integer limits ...
    c = 2147483647 + 0;
    printf("2147483647 + 0 = %d\n", c);  // 2147483647
    c = 2147483647 + 1;
    // erroneous results
    printf("2147483647 + 1 = %d\n", c);  // -2147483648  ?
    c = 2147483647 * 3;
    printf("2147483647 * 3 = %d\n", c);  // 2147483645  ?
    getchar();  // console wait
    return 0;
}

{% endhighlight %}

Python uses a different approach. When a variable is initialized with an integer value, that value becomes an integer object, and the variable points to it (references the object). Notice that the Python code and the C code have a lot of similarities, but the variable declarations are missing:

{% highlight python %}

# in Python you don't have to declare the variables
# Python gets the needed information during the initialization of the variables
# a, b, c all point to integer object 3
a = b = c = 3
print "a = %d  b = %d  c = %d" % (a, b, c)  # a = 3  b = 3  c = 3
print "a at %d  b at %d c at %d" % (id(a), id(b), id(c))  # a at 9721064  b at 9721064 c at 9721064
a = 4
print "a = %d  b = %d  c = %d" % (a, b, c)  # a = 4  b = 3  c = 3
# address pointed to by 'a' has changed, the object there is 4
print "a at %d  b at %d c at %d" % (id(a), id(b), id(c))  # a at 9721052  b at 9721064 c at 9721064
# a++ would increment the address and doesn't make much sense
# this construct is not allowed in Python
# however a = a + 1  or  a += 1 increments the integer object
a += 1
print "a = %d" % a   # a = 5
# swap two variables
a = 77
b = 99
print "before swap: a = %d  b = %d" % (a, b)
# swap, no temporary variable is needed
a, b = b, a
print "after swap:  a = %d  b = %d" % (a, b)
# exceeding the 32bit integer object
c = 2147483647 + 0
print "2147483647 + 0 = %d  type = %s" % (c, type(c))  # 2147483647  type = <type 'int'>
c = 2147483647 + 1
# no erroneous result
# simply switches type 32bit int to memory limited long
print "2147483647 + 1 = %d  type = %s" % (c, type(c))  # 2147483648  type = <type 'long'>
c = 2147483647 * 3
print "2147483647 * 3 = %d  type = %s" % (c, type(c))  # 6442450941  type = <type 'long'>
print
# a little extra information Python can easily give you ...
print "show a dictionary representing the current global symbol table:"
print globals()
raw_input()  # console wait

{% endhighlight %}

Note: Your memory locations might differ.

As you look at the result of the C code, and figure out the difference between the memory locations if int a and int b, you come to the conclusion that they are 4 bytes apart. In fact, an integer in C takes up 4 bytes or 32 bits. The maximum value of a C integer can then be 2^32 = 4,294,967,296 - 1 (-1 since we start with zero for the unsigned integer). If we want to include negative values, then we have to declare a signed (signed is default) integer with values from -2,147,483,648 to + 2,147,483,647. If you limit yourself to a number 9 digits long, you are pretty safe! Sometimes you see long instead of int, they are the same thing. Long is there to distinguish from short int, limited to 16 bits.

Python removes this confusion, there is only the integer object. Does it have any limits? Very early versions of Python had a limit that was later removed. The limits now are set by the amount of memory you have in your computer. If you want to create an astronomical integer 5,000 digits long, go ahead. Typing it or reading it will be the only problem! How does Python do all of this? It automatically manages the integer object, which is initially set to 32 bits for speed. If it exceeds 32 bits, then Python increases its size as needed up to the RAM limit.
