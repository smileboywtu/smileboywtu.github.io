---
layout: post
title:  git submodule usage
date:   2016-04-24 14:31:00 +0800
categories: articles
---

when you want to create a new branch for your product, you need to develop on a
new branch, with which you can develop very new feature for it.


first you need a base, check out to the branch and create a new branch

{% highlight shell %}
git checkout master
git checkout -b feature
{% endhighlight %}


add submodule or switch the branch or the submodule

{% highlight shell %}
git submodule add -b feature https://.....
" or switch the submodule branch
cd submodule-A && git checkout [-b] branch-name
{% endhighlight %}


commit your change:

{% highlight shell %}
git add submodule-A
git commit -am 'you commit message'
git push
{% endhighlight %}

## reference

- [stackoverflow](http://stackoverflow.com/questions/1777854/git-submodules-specify-a-branch-tag)
- [komodoIDE](http://komodoide.com/blog/2014-05/git-submodules/)
