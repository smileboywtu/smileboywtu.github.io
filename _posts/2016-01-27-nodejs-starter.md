---
layout: post
title: 'Node.js, Require and Exports'
date: '2016-01-27 17:29:00 +0800'
categories: articles
---

> **source article comes from**:
>
> [http://openmymind.net/2012/2/3/Node-Require-and-Exports/](http://openmymind.net/2012/2/3/Node-Require-and-Exports/)

Back when I first started playing with node.js, there was one thing that always made me uncomfortable. Embarrassingly, I'm talking about module.exports. I say embarrassingly because it's such a fundamental part of node.js and it's quite simple. In fact, looking back, I have no idea what my hang up was...I just remember being fuzzy on it. Assuming I'm not the only one who's had to take a second, and third, look at it before it finally started sinking in, I thought I could do a little write up.

In Node, things are only visible to other things in the same file. By things, I mean variables, functions, classes and class members. So, given a file misc.js with the following contents:

{% highlight js %}

var x = 5; var addX = function(value) {   return value + x; };

{% endhighlight %}

Another file cannot access the x variable or addX function. This has nothing to do with the use of the var keyword. Rather, the fundamental Node building block is called a module which maps directly to a file. So we could say that the above file corresponds to a module named file1 and everything within that module (or any module) is private.

Now, before we look at how to expose things out of a module, let's look at loading a module. This is where require comes in. require is used to load a module, which is why its return value is typically assigned to a variable:

{% highlight js %}

var misc = require('./misc');

{% endhighlight %}

Of course, as long as our module doesn't expose anything, the above isn't very useful. To expose things we use module.exports and export everything we want:

{% highlight js %}

var x = 5; var addX = function(value) {   return value + x; };

module.exports.x = x; module.exports.addX = addX;

{% endhighlight %}

Now we can use our loaded module:

{% highlight js %}

var misc = require('./misc'); console.log("Adding %d to 10 gives us %d", misc.x, misc.addX(10));

{% endhighlight %} There's another way to expose things in a module:

{% highlight js %} var User = function(name, email) {   this.name = name;   this.email = email; }; module.exports = User; {% endhighlight %}

The difference is subtle but important. See it? We are exporting user directly, without any indirection. The difference between:

{% highlight js %} module.exports.User = User; //vs module.exports = User; is all about how it's used:

var user = require('./user');

var u = new user.User(); //vs var u = new user(); {% endhighlight %} It's pretty much a matter of whether your module is a container of exported values or not. You can actually mix the two within the same module, but I think that leads to a pretty ugly API.

Finally, the last thing to consider is what happens when you directly export a function:

{% highlight js %} var powerLevel = function(level) {   return level > 9000 ? "it's over 9000!!!" : level; }; module.exports = powerLevel; {% endhighlight %} When you require the above file, the returned value is the actual function. This means that you can do:

{% highlight js %} require('./powerlevel')(9050); {% endhighlight %}

Which is really just a condensed version of:

{% highlight js %} var powerLevel = require('./powerlevel') powerLevel(9050); {% endhighlight %}

Hope that helps!
