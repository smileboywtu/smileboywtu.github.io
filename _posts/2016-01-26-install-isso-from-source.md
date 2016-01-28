---
layout: post
title: install isso from source
date: '2016-01-26 14:32:00 +0800'
categories: articles
---

# Introduction
isso is a python developed, self-hosted comment system, it can help you manage the comments and send email to notify you on the new comments.

the source code of the isso is host on [github]["isso-github"].

# How to
- here is the official tutorial [here]["official tutorial"]
- this article is my details to install the **isso** from source code.

# Install from source
- install sqlite3(make sure your version is larger than 3.3.8).
- install nodejs and npm, bower.
- install the python isso module in a virtual env.
- compile the source js for your client end.

here is installation passed from the python2.7 version, node 0.10.41, npm 1.3.10.

**1.** install the sqlite3, python2.7, and virtual env.

{% highlight bash %}
sudo apt-get install sqlite3 python2.7 python2-pip
sudo pip install virtualenv virtualenvwrapper
{% endhighlight %}

**2.** install the isso from source

{% highlight bash %}
# config the virtual env wrapper envirionment
# add this to your .bashrc

export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python2
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh

# create the directory
mkdir ~/.virtualenvs

# restart the bash and create the virtual env
mkvirtualenv isso

# now you should in the isso virtual env
# download or clone this repository.
git clone https://github.com/smileboywtu/isso.git

# install python isso module
cd isso
python setup.py install

{% endhighlight %}

**3.** compile the js file

{% highlight bash %}
# an easy easy way to install node and npm with apt
sudo apt-get install node npm

# or you can follow the steps of the official nodejs website.
curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
sudo apt-get install -y nodejs

# or install npm manually
curl -L https://www.npmjs.com/install.sh | sudo sh

# create a link on ubuntu
sudo ln -s /usr/bin/nodejs /usr/bin/node

# install bower after you have install node and npm
npm install -g bower

# inside the directory where the makefile exist.
make init

# check dependency
# you need to install all dependency installed under the python2.7
# version. make sure you install the dependency with the required
# version.
make check

# compile js code from source.
# this will compile the embed.min.js for your client side.
# if you can't see the embed.min.js inside the isso/isso/js after
# this command, you may get error of compilation.
# as I look details about the source code of isso, the isso just
# reference the js directory to find out the js file, so do not
# move or delect the source directory.
make js
{% endhighlight %}

**4.** run and test

{% highlight bash %}
# inside the isso virtual env
# use workon isso to shift to the isso virtual env you created.
isso run

# open the simple index.html inside the source directory.
# follow the official website for more usage details.
# if you can't comment or get some errors, just use the
# browser's develop tool to detect the error or contact
# other people like me to help you out.
{% endhighlight %}

["isso-github"]: https://github.com/posativ/isso
["official tutorial"]: https://posativ.org/isso/docs/
