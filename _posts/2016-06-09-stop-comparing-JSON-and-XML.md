---
layout: post
title:	stop comparing JSON and XML
date:	2016-06-09 13:50:00 +0800
categories: articles
---

They are very different things with their own areas of applicability.

Here is how a simple JSON piece of data may look (140 characters):

{% highlight json %}
{
  "id": 123,
  "title": "Object Thinking",
  "author": "David West",
  "published": {
    "by": "Microsoft Press",
    "year": 2004
  }
}
{% endhighlight %}

A similar document would look like this in XML (167 characters):

{% highlight xml %}
<?xml version="1.0"?>
<book id="123">
  <title>Object Thinking</title>
  <author>David West</author>
  <published>
    <by>Microsoft Press</by>
    <year>2004</year>
  </published>
</book>
{% endhighlight %}

Looks easy to compare, right? The first example is a bit shorter, 
is easier to understand since it's less "cryptic," and is also perfectly 
parseable in JavaScript. That's it, then; let's use JSON and manifest 
the death of XML! Who needs this heavyweight 15-year-old XML in the first place?

> JSON is a good data format, and it is just a data format

Well, I need it, and I love it. Let me explain why.

And don't get me wrong; I'm not against JSON. Not at all. 
It's a good data format. But it's just a data format. We're 
using it temporarily to transfer a piece of data from point 
A to point B. Indeed, it is shorter than XML and more readable. 
That's it.

XML is not a data format; it is a language. A very powerful one. 
Let me show you what it's capable of. Let me basically explain 
why I love it. And I would strongly recommend you read XML in a 
Nutshell, Third Edition by Elliotte Rusty Harold and W. Scott Means.

I believe there are four features XML has that seriously set it 
apart from JSON or any other simple data format, like YAML for example.

- **XPath**. To get data like the year of publication from the document above, 
I just send an XPath query: /book/published/year/text(). However, there has 
to be an XPath processor that understands my request and returns 2004. The 
beauty of this is that XPath 2.0 is a very powerful query engine with its 
own functions, predicates, axes, etc. You can literally put any logic into 
your XPath request without writing any traversing logic in Java, for example. 
You may ask "How many books were published by David West in 2004?" and get 
an answer, just via XPath. JSON is not even close to this.

- **Attributes and Namespaces**. You can attach metadata to your data, just like 
it's done above with the id attribute. The data stays inside elements, just 
like the name of the book author, for example, while metadata (data about data) 
can and should be placed into attributes. This significantly helps in organizing 
and structuring information. On top of that, both elements and attributes can 
be marked as belonging to certain namespaces. This is a very useful technique 
during times when a few applications are working with the same XML document.

- **XML Schema**. When you create an XML document in one place, modify it a few 
times somewhere else, and then transfer it to yet another place, you want to 
make sure its structure is not broken by any of these actions. One of them may 
use <year> to store the publication date while another uses <date> with ISO-8601. 
To avoid that mess in structure, create a supplementary document, which is called 
XML Schema, and ship it together with the main document. Everyone who wants to work 
with the main document will first validate its correctness using the schema supplied. 
This is a sort of integration testing in production. RelaxNG is a similar but simpler 
mechanism; give it a try if you find XML Schema too complex.

- **XSL**. You can make modifications to your XML document without any Java/Ruby/etc. 
code at all. Just create an XSL transformation document and "apply" it to your original 
XML. As an output, you will get a new XML. The XSL language (it is purely functional, 
by the way) is designed for hierarchical data manipulations. It is much more suitable 
for this task than Java or any other OOP/procedural approach. You can transform an XML 
document into anything, including plain text and HTML. Some complain about XSL's complexity, 
but please give it a try. You won't need all of it, while its core functionality is 
pretty straight-forward.

This is not a full list, but these four features really mean a lot to me. They give 
my document the ability to be "self-sufficient." It can validate itself (XML Schema), 
it knows how to modify itself (XSL), and it gives me very convenient access to anything 
inside it (XPath).

There are many more languages, standards, and applications developed around XML, 
including XForms, SVG, MathML, RDF, OWL, WSDL, etc. But you are less likely to 
use them in a mainstream project, as they are rather "niche."

JSON was not designed to have such features, even though some of them are now 
trying to find their places in the JSON world, including JSONPath for querying, 
some tools for transformations, and json-schema for validation. But they are just 
weak parodies compared to what XML offers, and I don't think they have any future. 
Or let's put it this way: I wish they would disappear sooner or later. They just 
turn a good, simple format into something clumsy.

Thus, to conclude, JSON is a simple data format with no additional functionality. 
Its best-use case is AJAX. In all other cases, I strongly recommend you use XML.

## ref

[blog](http://www.yegor256.com/2015/11/16/json-vs-xml.html)