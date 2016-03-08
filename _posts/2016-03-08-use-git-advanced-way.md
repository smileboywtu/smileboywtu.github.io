---
layout: post
title:  use the git advanced way
date:   2016-03-08 15:20:00 +0800
categories: articles
---

> **futher**:
>
> [http://stackoverflow.com/questions/67699/clone-all-remote-branches-with-git](http://stackoverflow.com/questions/67699/clone-all-remote-branches-with-git)

git is a very powerful and team cooperation tool using in you primary work.
here are what I learn these day.

after you entering a company, you may have to develop a product hold by the
company, so you need to develop based on the previous code.

**1.** fork the code from the center repository into your repository.

**2.** clone the code from your own repository into local dirs.

**3.** after that you may use git remote -v to watch your default push or
pull repository, you need to add the center repository to your remote. this
way you can get the newest code from your team, you can push the code into
your own repository and create a merge request.use git remote add center git@..

**4.** get all the branch information
{% highlight shell %}
git fetch origin/center
git branch -a
{% endhighlight %}

**5.** get all the branches into local:
{% highlight shell %}
git checkout -b branch_name origin/branch_name
{% endhighlight %}

**6.** after you create all the branch locally, you can git checkout a local
branch and then pull the newest code from center.

**7.** now it's the time you can develop some code.

**8.** after done, just push the code to origin and then create the merge request
online.

Happy coding.
