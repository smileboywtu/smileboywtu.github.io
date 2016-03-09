---
layout: post
title:  install pyqt and sip on mac
date:   2016-03-09 18:04:00 +0800
categories: articles
---

if you want to instal the pyqt, a good way is to install the package from the
source package.

here you need to install the sip and pyqt.

+ sip
+ pyqt

## install the sip

**1.** download the sip package

**2.** extract it, the go into the dir
{% highlight shell %}

python configure.py
make
make install

{% endhighlight %}

**3.** find the python of the sip

{% highlight shell %}

mdfind -name sip | grep '/bin/'

{% endhighlight %}

## install the pyqt

**1.** download the pyqt tar

**2.** extract the tar into dir

**3.** configure and install

{% highlight shell %}
python configure.py --sip sip_dir
make
make install
{% endhighlight %}
