---
layout: post
title:  using cors in python flask
date:   2016-02-18 11:08:00 +0800
categories: articles
---

**Cross-Origin Resource Sharing (CORS)** is a powerful technology for static
web apps. To understand what it is and why it's important, you first need to
understand a bit about how browsers work.

## same-origin policy

*The Same-Origin Policy* Under the policy, a web browser permits scripts
contained in a first web page to access data in a second web page, but only
if both web pages have the same origin. An origin is defined as a combination
of URI scheme, hostname, and port number. This policy prevents a malicious
script on one page from obtaining access to sensitive data on another web page
through that page's Document Object Model.

*The Same-Origin Policy* is a vital piece of web security architecture, but it
also poses a problem. What happens when you want to allow a site with a different
origin to access your content? For example, what if you're providing a JSON API
for third-party access, or even just for your own use?

## cross-origin resource Sharing

A resource makes a cross-origin HTTP request when it requests a resource from
a different domain than the one which the first resource itself serves.
For example, an HTML page served from website A makes an <img> src request for
website B. Many pages on the web today load resources like CSS stylesheets,
images and scripts from separate domains.

The Cross-Origin Resource Sharing standard works by adding new HTTP headers
that allow servers to describe the set of origins that are permitted to read
that information using a web browser.  Additionally, for HTTP request methods
that can cause side-effects on user data (in particular, for HTTP methods other
than GET, or for POST usage with certain MIME types), the specification
mandates that browsers "preflight" the request, soliciting supported methods
from the server with an HTTP OPTIONS request method, and then, upon "approval"
from the server, sending the actual request with the actual HTTP request method.
Servers can also notify clients whether "credentials" (including Cookies
and HTTP Authentication data) should be sent with requests.

## python flask cors example:

Here is a flask cors decorator from pocoo:

{% highlight python %}

from datetime import timedelta
from flask import make_response, request, current_app
from functools import update_wrapper


def crossdomain(origin=None, methods=None, headers=None,
                max_age=21600, attach_to_all=True,
                automatic_options=True):
    if methods is not None:
        methods = ', '.join(sorted(x.upper() for x in methods))
    if headers is not None and not isinstance(headers, basestring):
        headers = ', '.join(x.upper() for x in headers)
    if not isinstance(origin, basestring):
        origin = ', '.join(origin)
    if isinstance(max_age, timedelta):
        max_age = max_age.total_seconds()

    def get_methods():
        if methods is not None:
            return methods

        options_resp = current_app.make_default_options_response()
        return options_resp.headers['allow']

    def decorator(f):
        def wrapped_function(*args, **kwargs):
            if automatic_options and request.method == 'OPTIONS':
                resp = current_app.make_default_options_response()
            else:
                resp = make_response(f(*args, **kwargs))
            if not attach_to_all and request.method != 'OPTIONS':
                return resp

            h = resp.headers

            h['Access-Control-Allow-Origin'] = origin
            h['Access-Control-Allow-Methods'] = get_methods()
            h['Access-Control-Max-Age'] = str(max_age)
            if headers is not None:
                h['Access-Control-Allow-Headers'] = headers
            return resp

        f.provide_automatic_options = False
        return update_wrapper(wrapped_function, f)
    return decorator

{% endhighlight %}

this technology is used in this website.

## reference

1. ["Decorator for the HTTP Access Control"](http://flask.pocoo.org/snippets/56/)
2. ["HTTP access control "](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS)
3. ["Cross-Domain Requests with CORS"](https://staticapps.org/articles/cross-domain-requests-with-cors/)

Happy coding.
