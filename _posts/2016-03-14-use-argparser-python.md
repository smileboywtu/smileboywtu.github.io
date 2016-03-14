---
layout: post
title:  use argparser in python
date:   2016-03-14 16:22:00 +0800
categories: articles
---

programs always need arguments to make itself smarter to users. a program may
need the user's choice when run foreground or background.

so it's useful to add the arguments to your program.

when you using python language, you can find the doc in the official site, but
trust me that you may get confused even you follow the document.

here is the flow of my study and tricks.

let's start with a simple example:

{% highlight python %}

import argparse

parser = argparse.ArgumentParser()
parser.add_argument("-q", "--quit", help="start the assistant without gui",
                    action='store_true')

args = parser.parse_args()

if args.quit:
    print 'quit the program.'

{% endhighlight %}

when you start your program you may get this:

{% highlight shell %}

➜  argparser python main.py -q
quit the program.

➜  argparser python main.py -h
usage: main.py [-h] [-q]

optional arguments:
  -h, --help  show this help message and exit
  -q, --quit  start the assistant without gui

{% endhighlight %}

very easy, or right, but it's not always come to your thoughts when using python.

here we start to use sub module. supposing that we have a program can take:

- update
- download
- transfer
- delete

here we need arguments in the subcommands, we may define our argument like this:

{% highlight python %}

import argparse

parser = argparse.ArgumentParser()
parser.add_argument("-q", "--quit", help="start the assistant without gui",
                    action='store_true')

sub_parsers = parser.add_subparsers(help="sub parser for test.")

parser_a = sub_parsers.add_parser('a', help='a help')
parser_a.add_argument('bar', type=int, help='bar help')

parser_b = sub_parsers.add_parser('b', help='b help')
parser_b.add_argument('--baz', choices='XYZ', help='baz help')

args = parser.parse_args()

{% endhighlight %}

there seem no problem here, but when you start run the script, you may find it
not work as you think.

{% highlight shell %}

➜  argparser python main.py -h
usage: main.py [-h] [-q] {a,b} ...

positional arguments:
  {a,b}       sub parser for test.
    a         a help
    b         b help

optional arguments:
  -h, --help  show this help message and exit
  -q, --quit  start the assistant without gui

➜  argparser python main.py -q
usage: main.py [-h] [-q] {a,b} ...
main.py: error: too few arguments

➜  argparser python main.py -q a 1
quit the program.

{% endhighlight %}

why we make the -q the first level argument, it's equal to a and b, but when we
get -q why does the argparse tell us too few arguments?

so here i just tell you about that:

- if you want your first level flag work with your subcommand, you just add all
  the argurments and parse them at final step.
- if you want to make your first level arguments work at the same weight with
  your subcommands, you need to get them out first and then.

let's talk with second situation:

{% highlight python %}

import argparse

parser = argparse.ArgumentParser()
parser.add_argument("-q", "--quit", help="start the assistant without gui",
                    action='store_true')

# do the check here
parsed, remain = parser.parse_known_args(args)

if parsed.quit:
  print "quit the program"

if len(remain):

    sub_parsers = parser.add_subparsers(help="sub parser for test.")

    parser_a = sub_parsers.add_parser('a', help='a help')
    parser_a.add_argument('bar', type=int, help='bar help')

    parser_b = sub_parsers.add_parser('b', help='b help')
    parser_b.add_argument('--baz', choices='XYZ', help='baz help')

    parsed, remain = parser.parse_known_args(remain)

    if parsed.a:
        print 'get value: ', a

{% endhighlight %}

that's all I know about if you want to see more, you need to refer the docs.

# reference

- [python doc](https://docs.python.org/2.7/library/argparse.html)
- [stackoverflow](http://stackoverflow.com/questions/24207750/python-2-x-optionnal-subparsers-error-too-few-arguments)
