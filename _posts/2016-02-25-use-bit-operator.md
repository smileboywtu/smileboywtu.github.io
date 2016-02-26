---
layout: post
title:  use bit operator
date:   2016-02-25 17:33:00 +0800
categories: articles
---

> **source**:
>
> [http://www.toves.org/books/bitops/](http://www.toves.org/books/bitops)

<h2>Contents</h2>

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

<h2>Bits As Flag</h2>

So why would one want to use bitwise operators? One of the most important applications is to use an integer to hold a set of flags, where each bit corresponds to a different “fact.” A prime example of this comes from the Linux file system, where each file has a number called its mode that indicates who can access the file. If the 4's bit is set, then anybody can read the file; if the 2's bit is set, then anybody can modify the file; if the 128's bit is set, then the person who “owns” the file can modify it; and if the 256's bit is set, then the person who “owns” the file can read it. (The meanings for other bits are more complex.)

You might retrieve this integer in a program, but inevitably you will want to decode it. For example, what if our C program wants to check whether the owner has permission to modify a file? We would want to check whether the 128's bit is set. The most straightforward way to do this is to use the AND operator.

{% highlight c %}

if( (mode & 128) != 0 ) {
        ;
}

{% endhighlight %}

By doing a bitwise AND with 128, we are making it so that only the 128's bit will remain — all other bits in 128 are 0, and anything ANDed with 0 is 0. The result will be 0 if mode's 128's bit is 0, since all other bits become 0 when ANDed with 128; and if mode's 128's bit is 1, the result will be nonzero. So our if statement compares the result of the bitwise AND with zero.

To a casual observer, the appearance of 128 here seems quite meaningless. So the header files define constants that correspond to each of the bits to allow us to refer to the bits in a less obscure way. For owner write permission, the constant 128 is named S_IWUSR — the W stands for write permission, and USR stands for the user; the S and I are simply to ensure that the constant's name is distinct from all other constants' names. So we'd instead write the following.

{% highlight c %}

if( (mode & S_IWUSER) != 0 ) {
    ;
}

{% endhighlight %}

When we create a new file, we might want to indicate the permissions for this new file. For this, we would use the OR operator to combine several bits together.

{% highlight c %}

creat("new_file.txt",
    S_IWUSR | S_IRUSR | S_IROTH);

{% endhighlight %}

(Yes, the function is named creat. Ken Thompson was once asked if he would do anything differently if he were to design UNIX again. He responded, “I'd spell creat with an e.”)

This application of binary representation and bit operators extends beyond just file permissions. Three additional examples: An integer representation can indicate which of the mouse buttons and modifier keys are down, so when a program is responding to pressing a mouse button, it can retrieve a single number indicating the current combination, like control-right-click or shift-alt-left-click. Java's libraries also use flags for indicating whether a font is plain, bold, italic, or bold italic. And Python's re library uses flags for options on how regular expressions should be interpreted.

<h2>Count One Bit</h2>

Let's now ponder a popular programmers' puzzler: Write a function countOnes() that, given an int value, returns the number of bits in that number that are set to 1. For example, countOnes(21) should return 3, since 21's binary representation 10101(2) has three one bits in it. In fact, I visited Microsoft once interviewing for an internship, and one interviewer spent our appointment discussing various solutions to this question. (Many software companies like to spend interviews gauging interest and mastery using problems like this.)

<h3> simple technique </h3>

The simplest technique is right-shift the number 32 times, counting the number of times you right-shift a one bit off.

{% highlight c %}

int countOnesA(int num) {
    int ret = 0;
    int cur = num;
    int i;
    for (i = 0; i < 32; i++) {
        ret += cur & 1;
        cur >>= 1;
    }
    return ret;
}

{% endhighlight %}

Note the importance of the parentheses in the if condition: If they were omitted, then the compiler would interpret the expression as “num & (mask != 0),” since the operator hierarchy places != above &. Because C treats Boolean expressions as integers, this would compile fine, but it would not be what is desired. Many programmers always place parentheses around the Boolean operators because their precedence can result is such unexpected behavior.

<h3>clever technique</h3>

In my interview at Microsoft, I composed essentially countOnesB(), which impressed the interviewer well enough. But then he showed me a different technique, which I have to admit is pretty clever. It begins with the observation that, when you subtract a number by 1, all of the lowest bits change up to and including the lowest 1 bit; but the rest of the bits stay the same. So if I do a bitwise AND of n with n − 1, essentially I will remove the last one bit from n.

Once we observe this, we have only to write code that counts how many times we can remove the final bit in this way before we reach a number with no 1 bits at all (i.e., 0).

{% highlight c %}

int countOnesC(int num) {
    int ret = 0;
    int cur = num;
    while (cur != 0) {
        cur &= cur - 1;
        ret++;
    }
    return ret;
}

{% endhighlight %}

<h3>a cleverer technique</h3>

Much later, I heard of yet another technique, which is yet more clever.

{% highlight c %}

int countOnesD(int num) {
    int ret;
    ret = (num & 0x55555555)
        + ((num >> 1) & 0x55555555);
    ret = (ret & 0x33333333)
        + ((ret >> 2) & 0x33333333);
    ret = (ret & 0x0F0F0F0F)
        + ((ret >> 4) & 0x0F0F0F0F);
    ret = (ret & 0x00FF00FF)
        + ((ret >> 8) & 0x00FF00FF);
    ret = (ret & 0x0000FFFF)
        + ((ret >> 16) & 0x0000FFFF);
    return ret;
}

{% endhighlight %}

The first assignment statement begins with num & 0x55555555. The appearance of 0x55555555 may seem rather random, but recall that its binary representation alternates between zeroes and ones: 0101010101…. The bitwise AND of num with this number gives a version of num where every other bit (the 2's bit, the 8's bit, the 32's bit) has been removed and the remaining bits (1's, 4's, 16's, …) are kept. We'll call this result a.

The right side of this first addition, which we'll call b, involves right-shifting num once and then finding the bitwise AND of this. The result includes all the bits omitted from a, but shifted so that they they line up with a's bits, in the 1's place, 4's place, 16's place, and so on.

This first addition then adds these two results a and b together. The result, placed into ret, has each pair of bits holding the sum of that pair of bits in the original value of num. Let's look at an 8-bit example, 01110110.

{% highlight c %}

num = 0x72;             // 01110010
a = num & 0x55;	        // 01010000
b = (num >> 1) & 0x55;	// 00010001
ret = a + b;	        // 01100001

{% endhighlight %}

The first two bits from num are 0 and 1, and the first two bits from the result are 01, which is 0 + 1. The next two bits from num are 1 and 1, and the next two bits from the result are 10, which is 1 + 1. And so on.

Notice what has happened here: In a single 32-bit addition operation, we have managed to perform 16 different bit-additions at the same time.

Now we proceed to add pairs of bits. Finding ret & 0x33333333 zeroes out every other pair of bits, leaving only the bottom pair (1's and 2's places), the third pair from the bottom (16's and 32's places), and so on. And (ret >> 2) & 0x33333333 finds all the remaining pairs, shifted so they are parallel to ret & 0x33333333. We now add these two groups of pairs together, and we end up with each group of four bits containing the sum of the two pairs previously in those four bits. Let's continue with our example.

{% highlight c %}

ret = 0x61;             // 01100001
a = ret & 0x33;	        // 00100001
b = (ret >> 2) & 0x33;	// 00010000
ret = a + b;	        // 00110001

{% endhighlight %}

Now the first four bits (0011) contain the sum of the first pair of bits from the original ret (01) and the second pair (10); and the second four bits (0001) contain the sum of the third pair (00) and the fourth pair (01). In the original value of num, the three of the first four bits were 1, and now the first four bits contain the number 3 in binary; likewise, the lower four bits in the original num contained one 1 bit, and now the lower four bits contain the number 1 in binary. This is not a coincidence.

The countOnesD() function continues, adding the groups of four bits together, then the groups of eight bits together, and finally the two groups of sixteen bits together. At the end, we have the total number of 1 bits in the original number.
