---
layout: post
title:  use bit operator
date:   2016-02-25 17:33:00 +0800
categories: articles
---

<h2>contents</h2>

In this document, we'll study how a program can manipulate computers' integer
representation directly using bit operators. This is sometimes pejoratively
called “bit twiddling,” but expert developers would agree that mastering bit
operators is a key skill for proficient programmers.

<h3>bit operator</h3>

The most significant bit operators are the bitwise operators, which perform
logic operations on all bits at once. For example, the bitwise NOT operator
~ (also called the bitwise complement) takes the NOT of all the bits in an
integer. For example, if b holds the int value 00110111, then ~b flips all
these bits to arrive at the int value 11001000.

The other bitwise operators — bitwise AND &, bitwise OR |, and bitwise XOR ^ —
work with two operands. Each pairs corresponding bits in the two operands and
performing the logical operation on them. For example, to compute the bitwise
OR of 1100 and 1010, we take the OR of the first bit from each number (1, 1),
followed by the OR of the second bits (1, 0), then the third bits (0, 1), then
the fourth bits (0, 0). For each of these bit pairs, we include a 1 in the
result if the first bit or the second bit in the pair is 1 (or if both are one).
Thus, we end up with an output of 1110.

Similarly, the bitwise AND includes a 1 in the result if the first bit and the
second bit in the pair is 1; the bitwise AND of 1100 and 1010 ends up being 1000.
And the bitwise XOR (short for exclusive or) includes a 1 if the first bit or
the second bit is 1 (but excluding the case that both bits are 1); the bitwise
XOR of 1100 and 1010 ends up being 0110.

The following illustrates these operators at work.

{% highlight python %}

int a = 0x23;        /* 00100011 */
int b = 0x36;        /* 00110110 */
printf("%x\n", ~a);
  /* prints DC (hex for 11011100) */
printf("%x\n", a & b);
  /* prints 22 (hex for 00100010) */
printf("%x\n", a | b);
  /* prints 37 (hex for 00110111) */
printf("%x\n", a ^ b);
  /* prints 15 (hex for 00010101) */

{% endhighlight %}

<h3>shift operator</h3>

C provides two shift operators: the left-shift operator << and the right-shift
operator >>. These are binary operators, with the value to be shifted on the
left side of the operator, and the distance to shift the value on the right side.

For example, the value of 5 << 2 is 20, since the binary representation of 5 is
101(2), which we shift left two spaces (and fill the empty spots with zeroes) to
get 10100(2) = 20(10). Bits that go off the left end are simply lost. (Many
programmers would say they fall into the “bit bucket” (or, more verbosely,
the “great bit bucket in the sky”), a mythical location where bits go
when they disappear.)

Notice that left-shifting by n bits is equivalent to multiplying by 2n, since
we've essentially multiplied the value of each 1 bit in the number by that much.
In the example of left-shifting 5 by 2, we get 5 ⋅ 2² = 20.

The right-shift operator works analogously: The value of 20 >> 2 is 5, since
20's binary representation is 10100(2), which shifted right two spots is 101(2)
= 5. Again, the bits pushed off the right end simply fall into the bit bucket,
so 22(10) = 10111(2) shifted right twice is also 101(2) = 5. Note that
right-shifting is similar to dividing by a power of 2 (and ignoring the remainder).

C doesn't specify how to handle right-shifting negative numbers. There are two
common alternatives. In the first, called the logical right shift, a right-shift
of a negative number will inserts zeroes into the top end. The second, the
arithmetic right shift also does this for positive numbers and zero, but it
inserts one bits on the top end for a negative number. Arithmetic right shifts
are not as intuitive, but they correspond better to dividing by a power of 2
since negative numbers remain negative: If you take −24 and arithmetically
right-shift 2 places, you get −6. Because this is so useful, programmers usually
prefer it, and so most compilers do an arithmetic right shift for >>.

because programmers frequently use right-shift in place
(Java resolves this ambiguity by adding a logical right-shift operator >>> in
addition to the arithmetic right-shift operator >>. Thus if c holds
11…111101100 representing −20(10), a Java compiler would compute c >> 2 to
be 1111…1111011 representing −5(10), but it would compute c >>> 2 as
0011…1111011 representing 1073741822(10)).

<h3>operator hierarchy</h3>

The bit operators fit into the operator precedence hierarchy as follows.

{% highlight python %}

~ ! + - (unary operators)
* / %
+ -
<< >> (and in Java, >>>)
< > <= >=
== !=
&
^
|
&&
||
conditional operator (?:)
= *= /= %= += -= <<= >>= &= ^= |=

{% endhighlight %}

At the bottom level, you can see that the bitwise and shift operators can be
combined with the assignment operator, much like +=.
