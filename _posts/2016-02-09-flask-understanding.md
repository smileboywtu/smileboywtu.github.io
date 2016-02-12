---
layout: post
title:  flask route understanding
date:   2016-02-09 18:57:00 +0800
categories: articles
---

first let's look at the example code of python flask web framework.

{% highlight python %}

#!/usr/bin/env python

from flask import flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "hello, flask."

@app.route('/post/<int:post_id>')
def show_post(post_id):
    pass

if __name__ == '__main__':
    app.run()

{% endhighlight %}

here we use the app.route to complish the route to '/' and '/post/post_id'
here are map of url and it's callback(function).

{% highlight python %}

{
    '/': hello,
    '/post/<int:post_id>': show_post
}

{% endhighlight %}

after we define the router, the hello function will be invoked when the user
navigate to the 'http://base_url/' and show_post function will be invoked when
user navigate to the 'http://base_url/post/post_id'.

it's not hard to conclude that the flask route just build an relation between
the url and function.

the **WSGI** can be divided into there part according it's function:

1. server
2. middleware
3. application

the server listen to the port and get the request, after getting the request it
will invoke the middleware, the middleware will let the application to deal with
the request.

here is how WSGI deal with a http request:

1. server gets the request and form the environ and start_response, then invoke
    the middleware.

2. middleware will find the function according the environ formed by server, after
    that the middleware will invoke the application.

3. the application build the final response sent to user.

structure like this:

![Alt text](http://g.gravizo.com/g?

digraph stucture {

rankdir=LR;

http -> WSGI;
WSGI -> middleware;
middleware -> middleware;
middleware -> application;

})

{% highlight python %}

app = Flask(__name__)

{% endhighlight %}

the code above create a middleware and its main function is **delivering the
request**.

how can the middleware can deliver the request?

the main difficult is to build the relation between the request url and its
function.

we first implement the Flask class myself:

{% highlight python %}

class Flask(object):

    def __init__(self):
        self.url_map = {}

    def __call__(self, environ, start_response):
        self.dispatch_request()
        return application(environ, start_response)

    def route(self, rule):
        def decorator(f):
            self.url_map[rule] = f
            return f
        return decorator

    def dispatch_request(self):
        url = get_url_from_environ()
        return self.url_map[url]()

{% endhighlight %}

this is a very simple flask middleware, here we include the function:

+ meet the require of WSGI. can invoke the application.
+ save the map of url and function.
+ can find the function according to the url.

but the flask use the werkzeug lib to save the url and function information.
it's more complicated than we define.

In werkzeug lib, we use Rule to save the url, endpoint, function:

>(url, endpoint, function)

here use two separate dictionary to save the Rule.

- {url: endpoint}
- {endpoint: function}

start the flask server

{% highlight python %}
app.run()
{% endhighlight %}

this code invoke the WSGI server inside the python standard lib. whatever the
server is, the Flask.\_\_call\_\_ will be invoked finally. this function will
accomplish the dispatch task.

conclusion
----------
+ the Flask class is WSGI's dispatch middleware
+ Flask's url_map save all the (url, endpoint, function) relations
+ Flask's view_functions save all the {endpoint: function} relations
+ dispatch request first find the endpoint according to the url,
    then use endpoint to find out the function.

happy coding.
