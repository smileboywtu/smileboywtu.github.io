---
layout: post
title:  use ssh in your work
date:   2016-03-08 09:02:00 +0800
categories: articles
---

ssh is a very useful tool in your work, you can use this to connect to your
develop environment or config you github.

## check your existing public ssh key

the default ssh key is saved at the *~/.ssh* directory, so before you start to
use ssh, you need to check if you already has a public ssh key.

## generate ssh key in you home

**1.** generate ssh key
{% highlight shell %}
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
{% endhighlight %}  
**2.** start the ssh agent
{% highlight shell %}
eval "$(ssh-agent -s)"
{% endhighlight %}
**3.** add the ssh key to ssh agent
{% highlight shell %}
ssh-add ~/.ssh/id_rsa
{% endhighlight %}
**4.** test your ssh
{% highlight shell %}
ssh -T git@github.com
{% endhighlight %}

## add the ssh key to your github

after you config the ssh to your github, it's easy to push the code to your
remote repository without input your user name and password every time.

**1.** head to the ssh directory
{% highlight shell %}
cd ~/.ssh/
ls .
cat *.pub
{% endhighlight %}
**2.** copy the key and add to your github ssh setting.

## use the config file when you connecting the develop environment.

when you connect to your remote ssh develop environment, like aliyun or other
ECS server, you will use the ssh. every time you need to input your remote
address and the username, it's not easy to remember the host address. so
use the ssh config file will help you.

**1.** create the config file at .ssh directory

{% highlight shell %}

cd ~/.ssh/
touch config
vim config

{% endhighlight %}

**2.** add your remove host information to the config file.

{% highlight shell %}

Host dev_name
  HostName *.*.*.*
  User  username

...

{% endhighlight %}

**3.** now you can connect your ssh develop just use the command

{% highlight shell %}

ssh dev_name

{% endhighlight %}

Happy Coding.
