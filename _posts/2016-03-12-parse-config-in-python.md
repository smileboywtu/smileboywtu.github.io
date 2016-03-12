---
layout: post
title:  parse config file in python
date:   2016-03-12 17:50:00 +0800
categories: articles
---

in python, you may need to parse the config file sometime in your work.
for example let the user config the app or server. you need to write config
file like .ini or xml file.

this article will show you how to use simple .ini file and parse it using
python.

there are library to help, before we start to use the library, we should know
a little about the config file.

{% highlight shell %}

" config.ini

[section]
key=value
.
.
.
[section]
key=value

{% endhighlight %}

when using the library **ConfigParser**, there are some method may confuse you.
something like: options, sections.

- options:  items under the section
- section:  this will appear in .ini file which surround with **[]**

we start with this simple config file:

{% highlight ini %}

[EDITOR]
pdf=adobe
word=office
ppt=office
[UNKNOW]
name=someone

{% endhighlight %}

now I will show you what's the section and option:

{% highlight python %}

def parse_config(config_file):
    """parse the config

    """
    parser = ConfigParser.ConfigParser()
    parser.read(config_file)

    # show the section
    print parser.sections()

    # show the of the section
    for section in parser.sections():
        print parser.options(section)

{% endhighlight %}

you will see the output here:

{% highlight shell %}

['EDITOR', 'UNKNOW']
['pdf', 'word', 'ppt']
['name']

{% endhighlight %}

get and value of the option:

{% highlight python %}

# get the value of the config
for section in parser.sections():
    print section
    for (key, value) in parser.items(section):
        print key, " : ", value

{% endhighlight %}

set the value of the option:

{% highlight python %}

parser.set(section, key, value)

{% endhighlight %}

save the config file at last:

{% highlight python %}

parser.write(open(config_file, 'w'))

{% endhighlight %}

to find more information about the usage, you can forward to the doc.

# reference

[https://docs.python.org/2/library/configparser.html](https://docs.python.org/2/library/configparser.html)
