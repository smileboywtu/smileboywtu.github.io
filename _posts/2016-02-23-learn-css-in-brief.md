---
layout: post
title:  learn css in brief
date:   2016-02-23 10:07:00 +0800
categories: articles
---

> **source**:
>
> [http://learnlayout.com/](http://learnlayout.com/)

<style type='text/css'>
    .property {
        background-color: #3333FF;
        color: white;
        padding: 3px;
        border-width: 3px;
        border-radius: 3px;
    }
</style>

css is very good language to decorate the html pages, It's very import to make
your website a beautiful or professional home for your product. the css makes
sense. but css is very large language, it has many properties to remember.

here I just find [Learn CSS Layout](http://learnlayout.com/) is a good and brief
website to make you go through the css style.

<span class="property">display</span>

value **"none"**. Some specialized elements such as script use this as their default. It is commonly used with JavaScript to hide and show elements without really deleting and recreating them.

This is different from visibility. Setting display to none will render the page as though the element does not exist. visibility: hidden; will hide the element, but the element will still take up the space it would if it was fully visible.

<span class='property'>margin</span>

{% highlight css %}

#main {
  width: 600px;
  margin: 0 auto;
}

{% endhighlight %}

Setting the width of a block-level element will prevent it from stretching out to the edges of its container to the left and right. Then, you can set the left and right margins to auto to horizontally center that element within its container. The element will take up the width you specify, then the remaining space will be split evenly between the two margins.

The only problem occurs when the browser window is narrower than the width of your element. The browser resolves this by creating a horizontal scrollbar on the page.

<span class="property">max-width</span>


{% highlight css %}

#main {
  max-width: 600px;
  margin: 0 auto;
}

{% endhighlight %}

Using max-width instead of width in this situation will improve the browser's handling of small windows. This is important when making a site usable on mobile. Resize this page to check it out!

By the way, max-width is supported by all major browsers including IE7+ so you shouldn't be afraid of using it.

<span class="property">the box model</span>

While we're talking about width, we should talk about width's big caveat: the box model. When you set the width of an element, the element can actually appear bigger than what you set: the element's border and padding will stretch out the element beyond the specified width. Look at the following example, where two elements with the same width value end up different sizes in the result.

{% highlight css %}

.simple {
  width: 500px;
  margin: 20px auto;
}

.fancy {
  width: 500px;
  margin: 20px auto;
  padding: 50px;
  border-width: 10px;
}

{% endhighlight %}

For generations, the solution to this problem has been extra math. CSS authors have always just written a smaller width value than what they wanted, subtracting out the padding and border. Thankfully, you don't have to do that anymore...

<span class="property">box-sizing</span>

The original box model behavior was eventually considered unintuitive, so a new CSS property called box-sizing was created. When you set box-sizing: border-box; on an element, the padding and border of that element no longer increase its width. Here is the same example as the previous page, but with box-sizing: border-box; on both elements:

{% highlight css %}

.simple {
  width: 500px;
  margin: 20px auto;
  -webkit-box-sizing: border-box;
     -moz-box-sizing: border-box;
          box-sizing: border-box;
}

.fancy {
  width: 500px;
  margin: 20px auto;
  padding: 50px;
  border: solid blue 10px;
  -webkit-box-sizing: border-box;
     -moz-box-sizing: border-box;
          box-sizing: border-box;
}

{% endhighlight %}

Since this is so much better, some authors want all elements on all their pages to always work this way. Such authors put the following CSS on their pages:

{% highlight css %}

* {
  -webkit-box-sizing: border-box;
     -moz-box-sizing: border-box;
          box-sizing: border-box;
}

{% endhighlight %}

This ensures that all elements are always sized in this more intuitive way.

Since box-sizing is pretty new, you should use the -webkit- and -moz- prefixes for now, as I have in these examples. This technique enables experimental features in specific browsers. Also, keep in mind that this one is IE8+.

<span class='property'>position</span>

In order to make more complex layouts, we need to discuss the position property. It has a bunch of possible values, and their names make no sense and are impossible to remember. Let's go through them one by one, but maybe you should bookmark this page too.

**static** is the default value. An element with position: static; is not positioned in any special way. A static element is said to be not positioned and an element with its position set to anything else is said to be positioned.

**relative** behaves the same as static unless you add some extra properties.
Setting the top, right, bottom, and left properties of a relatively-positioned element will cause it to be adjusted away from its normal position. Other content will not be adjusted to fit into any gap left by the element.

{% highlight css %}

.relative1 {
  position: relative;
}
.relative2 {
  position: relative;
  top: -20px;
  left: 20px;
  background-color: white;
  width: 500px;
}

{% endhighlight %}

A **fixed** element is positioned relative to the viewport, which means it always stays in the same place even if the page is scrolled. As with relative, the top, right, bottom, and left properties are used.

I'm sure you've noticed that fixed element in the lower-right hand corner of the page. I'm giving you permission to pay attention to it now. Here is the CSS that puts it there:

{% highlight css %}

.fixed {
  position: fixed;
  bottom: 0;
  right: 0;
  width: 200px;
  background-color: white;
}

{% endhighlight %}

A fixed element does not leave a gap in the page where it would normally have been located.

**absolute** is the trickiest position value. absolute behaves like fixed except relative to the nearest positioned ancestor instead of relative to the viewport. If an absolutely-positioned element has no positioned ancestors, it uses the document body, and still moves along with page scrolling. Remember, a "positioned" element is one whose position is anything except static.

Here is a simple example:

{% highlight css %}

.relative {
  position: relative;
  width: 600px;
  height: 400px;
}
.absolute {
  position: absolute;
  top: 120px;
  right: 0;
  width: 300px;
  height: 200px;
}

{% endhighlight %}

position example:

{% highlight css %}

.container {
  position: relative;
}
nav {
  position: absolute;
  left: 0px;
  width: 200px;
}
section {
  /* position is static by default */
  margin-left: 200px;
}
footer {
  position: fixed;
  bottom: 0;
  left: 0;
  height: 70px;
  background-color: white;
  width: 100%;
}
body {
  margin-bottom: 120px;
}

{% endhighlight %}

This example works because the container is taller than the nav. If it wasn't, the nav would overflow outside of its container. In the coming pages we'll discuss other layout techniques that have different pros and cons.

<span class="property">float</span>

Another CSS property used for layout is float. Float is intended for wrapping text around images, like this:

{% highlight css %}

img {
  float: right;
  margin: 0 0 1em 1em;
}

{% endhighlight %}

<span class='property'>clear</span>

The clear property is important for controlling the behavior of floats. Compare these two examples:

{% highlight html %}

<div class="box">...</div>
<section>...</section>

{% endhighlight %}

{% highlight css %}

.box {
  float: left;
  width: 200px;
  height: 100px;
  margin: 1em;
}

{% endhighlight %}

In this case, the section element is actually after the div. However, since the div is floated to the left, this is what happens: the text in the section is floated around the div and the section surrounds the whole thing. What if we wanted the section to actually appear after the floated element?

{% highlight css %}

.box {
  float: left;
  width: 200px;
  height: 100px;
  margin: 1em;
}
.after-box {
  clear: left;
}

{% endhighlight %}

Using clear we have now moved this section down below the floated div. You use the value left to clear elements floated to the left. You can also clear right and both.

<span class="property">the clearfix hack</span>

when you use the float property for the image maybe the image is overflow the container.
you can use the overflow property to fix this:

{% highlight css %}

.clearfix {
  overflow: auto;
}

{% endhighlight %}

<span class="property">float layout example</span>

It's very common to do entire layouts using float. Here is the same layout we did with position earlier, but using float instead.

{% highlight css %}

nav {
  float: left;
  width: 200px;
}
section {
  margin-left: 200px;
}

.clearfix {
    overflow: auto;
}

{% endhighlight %}

{% highlight html %}

<div class="clearfix">

    <nav> ... </nav>

    <section> ... </section>

    <section ... </section>

</div>

{% endhighlight %}


<span class='property'>percent width</span>

Percent is a measurement unit relative to the containing block. It's great for images: here we make an image that is always 50% the width of its container. Try shrinking down the page to see what happens!

You can use percent for layout, but this can require more work. In this example, the nav content starts to wrap in a displeasing way when the window is too narrow. It comes down to what works for your content.

{% highlight css %}

nav {
  float: left;
  width: 25%;
}
section {
  margin-left: 25%;
}

{% endhighlight %}

When this layout is too narrow, the nav gets squished. Worse, you can't use min-width on the nav to fix it, because the right column wouldn't respect it.

<span class='property'>media queries</span>

"Responsive Design" is the strategy of making a site that "responds" to the browser and device that it is being shown on... by looking awesome no matter what.

Media queries are the most powerful tool for doing this. Let's take our layout that uses percent widths and have it display in one column when the browser is too small to fit the menu in the sidebar:

{% highlight css %}

@media screen and (min-width:600px) {
  nav {
    float: left;
    width: 25%;
  }
  section {
    margin-left: 25%;
  }
}
@media screen and (max-width:599px) {
  nav li {
    display: inline;
  }
}

{% endhighlight %}

<span class='property'>inline-block</span>

You can create a grid of boxes that fills the browser width and wraps nicely. This has been possible for a long time using float, but now with inline-block it's even easier. inline-block elements are like inline elements but they can have a width and height.

{% highlight css %}

.box {
  float: left;
  width: 200px;
  height: 100px;
  margin: 1em;
}
.after-box {
  clear: left;
}

{% endhighlight %}

easy way to do this:

{% highlight css %}

.box2 {
  display: inline-block;
  width: 200px;
  height: 100px;
  margin: 1em;
}

{% endhighlight %}

<span class='property'>column</span>

There is a new set of CSS properties that let you easily make multi-column text. Have a look:

{% highlight css %}

.three-column {
  padding: 1em;
  -moz-column-count: 3;
  -moz-column-gap: 1em;
  -webkit-column-count: 3;
  -webkit-column-gap: 1em;
  column-count: 3;
  column-gap: 1em;
}

{% endhighlight %}

CSS columns are very new, so you need to use the prefixes, and it won't work through IE9 or in Opera Mini.

<span class='property'>flexbox</span>

The new flexbox layout mode is poised to redefine how we do layouts in CSS. Unfortunately the specification has changed a lot recently, so it's implemented differently in different browsers.

{% highlight css %}

.container {
  display: -webkit-flex;
  display: flex;
}
nav {
  width: 200px;
}
.flex-column {
  -webkit-flex: 1;
          flex: 1;
}

{% endhighlight %}

make the block vertical center:

{% highlight css %}

.vertical-container {
  height: 300px;
  display: -webkit-flex;
  display:         flex;
  -webkit-align-items: center;
          align-items: center;
  -webkit-justify-content: center;
          justify-content: center;
}

{% endhighlight %}

Happy code.
