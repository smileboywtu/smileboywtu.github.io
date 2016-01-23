---
layout: post
title:  "scrape web using scrapy and mongodb"
date:   2016-01-23 10:16:23 +0800
categories: articles
---

# prepare

you'd better do this in a virtual env.

* install mongodb

{% highlight python %}
    pip install pymongo
{% endhighlight %}

* install scrapy

{% highlight python %}
    pip install scrapy
{% endhighlight %}

# description

use scrapy to scrapy some new questions from stackoverflow and save the data into the mongodb.

# details

if you use the virutal env, make sure you are inside the environment.

{% highlight shell %}
    scrapy startproject stackoverflow
    cd stackoverflow
    tree .
    ├── scrapy.cfg
    └── stackoverflow
        ├── __init__.py
        ├── items.py
        ├── pipelines.py
        ├── settings.py
        └── spiders
            └── __init__.py

{% endhighlight %}

**1.** edit the items

*here we just record the title and url field.*

{% highlight python %}
# items.py
class StackoverflowItem(scrapy.Item):
    title = scrapy.Field()
    url = scrapy.Field()
{% endhighlight %}

**2.** create the spiders

*before we create the spiders, we need to find out what path
we will use to scrapy the data, if you are not familiar with
the xpath, you can find more detail in the xpath documents.
xpath like a file path in the file system, use xpath the
scrapy can find out the data we need.*

*you can use the web browser to help you find out the xpath.*

1. use firefox or chrome open the web you want to scrapy.
2. select the item you are interested in.
3. right mouse key and select inspect elements.
4. after you get the html tag, just right mouse key get the xpath.

*if you select this article title, and follow the step, you will find
the xpath of this site is: "/html/body/div/div/article/header/h1"*

*if you want to test if you get the correct xpath, you can use the browser
js console to test:*

![test xpath]({{ site.url }}/downloads/posts/scrape_stackoverflow/js_test_xpath.png)

**3.** ready to create the spiders

* here we just want to scrape the website:  
"http://stackoverflow.com/questions"
* we just want to get title and url item as definition in items.py.

![scrapy item]({{ site.url }}/downloads/posts/scrape_stackoverflow/scrapy_item.png)

* get the xpath of title:
"//\*[@id='question-summary-34959124']/div[2]/h3/a/text()"

> notice that: if you want to extract the title text out you need to append
text() function. please use the single quote inside the double quote.

* get the xpath of url:
"//\*[@id='question-summary-34959124']/div[2]/h3/a/@href"

> notice that: if you want to get the attribute out you need to use operator
@ before the attribute of html. please use the single quote inside
the double quote.

* inspect the source html structure
you may test successfully under the js console inside the browser, but you'll
get error when use the scrapy to scrapy the test out.

what you really want to do is to scrapy the whole list of the question, but now
you just scrapy only a specific one.

{% highlight html %}
<!-- question list -->
<div id="questions">
    <div class="question-summary"> ... </div>
    .
    .
    .
</div>

<!-- div for each question -->
<div class="summary">
    <h3>
        <a href="example.com">title</a>
    </h3>
</div>

{% endhighlight %}

**4.** write the spiders

here comes the source code, do more test in scrapy shell.

{% highlight python %}
# create spider.py inside the spiders/question_newest.py
# or you can use the scrapy to create the spider for you:
# scrapy genspider -t basic question_newest http://stackoverflow.com/questions
"""
    this spider crawl the title and url from the
    stackoverflow newest questions list.
"""

import scrapy
from stackoverflow.items import StackoverflowItem

stackoverflow_url = u"http://stackoverflow.com"
questions_xpath = "//div[@class='summary']/h3"


class NewestQuestionSpider(scrapy.Spider):
    name = "newest_question"
    allowed_domains = ["http://stackoverflow.com/questions"]
    start_urls = (
        'http://stackoverflow.com/questions',
    )

    def parse(self, response):
        questions = response.xpath(questions_xpath)
        for question in questions:
            item = StackoverflowItem()
            item['title'] = question.xpath("a/text()").extract()[0]
            item['url'] = stackoverflow_url + \
                question.xpath("a/@href").extract()[0]
            yield item

{% endhighlight %}

# test

{% highlight shell %}
# scrape the stackoverflow newest quesion and save it into the json file.
# if you do not use -o result.json. you won't get any output except log.
scrapy crawl question_newest -o result.json

{% endhighlight %}

![result json]({{ site.url }}/downloads/posts/scrape_stackoverflow/result.png)

Copyright (c) 2015 smileboywtu All Rights Reserved.
