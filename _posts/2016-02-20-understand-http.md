---
layout: post
title:  understand http workflow
date:   2016-02-20 17:29:00 +0800
categories: articles
---

\>\> <strong style="color:red;">what happens when I follow the link?</strong>

<strong style="color:blue;">Step 1: parsing the url</strong>

The first thing the browser has to do is to look at the URL of the new document to find out how to get hold of the new document. Most URLs have this basic form: "protocol://server/request-URI". The protocol part describes how to tell the server which document that you want and how to retrieve it. The server part tells the browser which server to contact, and the request-URI is the name used by the web server to identify the document. (I use the term request-URI since it's the one used by the HTTP standard, and I can't think of anything else that is general enough to not be misleading.)

<strong style="color:blue;">Step 2: sending the request</strong>

Usually, the protocol is "http". To retrieve a document via HTTP the browser transmits the following request to the server: "GET /request-URI HTTP/version", where version tells the server which HTTP version is used.

One important point here is that this request string is all the server ever sees. So the server doesn't care if the request came from a browser, a link checker, a validator, a search engine robot or if you typed it in manually. It just performs the request and returns the result.

<strong style="color:blue;">Step 3: the server response</strong>

When the server receives the HTTP request it locates the appropriate document and returns it. However, an HTTP response is required to have a particular form. It must look like this:

{% highlight http %}

HTTP/[VER] [CODE] [TEXT]
Field1: Value1
Field2: Value2

...Document content here...

{% endhighlight %}

The first line shows the HTTP version used, followed by a three-digit number (the HTTP status code) and a reason phrase meant for humans. Usually the code is 200 (which basically means that all is well) and the phrase "OK". The first line is followed by some lines called the header, which contains information about the document. The header ends with a blank line, followed by the document content. This is a typical header:

{% highlight http %}

HTTP/1.0 200 OK
Server: Netscape-Communications/1.1
Date: Tuesday, 25-Nov-97 01:22:04 GMT
Last-modified: Thursday, 20-Nov-97 10:44:53 GMT
Content-length: 6372
Content-type: text/html

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<HTML>
...followed by document content...

{% endhighlight %}

We see from the first line that the request was successful. The second line is optional and tells us that the server runs the Netscape Communications web server, version 1.1. We then get what the server thinks is the current date and when the document was modified last, followed by the size of the document in bytes and the most important field: "Content-type".

The content-type field is used by the browser to tell which format the document it receives is in. HTML is identified with "text/html", ordinary text with "text/plain", a GIF is "image/gif" and so on. The advantage of this is that the URL can have any ending and the browser will still get it right.

An important concept here is that to the browser, the server works as a black box. Ie: the browser requests a specific document and the document is either returned or an error message is returned. How the server produces the document remains unknown to the browser. This means that the server can read it from a file, run a program that generates it, compile it by parsing some kind of command file or (very unlikely, but in principle possible) have it dictated by the server administrator via speech recognition software. This gives the server administrator great freedom to experiment with different kinds of services as the users don't care (or even know) how pages are produced.

<strong style="color:blue;">HTTP Methods</strong>

all available methods:

+ **GET**:
The GET method is used to retrieve information from the given server using a given URI. Requests using GET should only retrieve data and should have no other effect on the data.

+ **HEAD**:
Same as GET, but transfers the status line and header section only.

+ **POST**:
A POST request is used to send data to the server, for example, customer information, file upload, etc. using HTML forms.

+ **PUT**:
Replaces all current representations of the target resource with the uploaded content.

+ **DELETE**:
Removes all current representations of the target resource given by a URI.

+ **CONNECT**:
Establishes a tunnel to the server identified by a given URI.

+ **OPTION**:
Describes the communication options for the target resource.

+ **TRACE**:
Performs a message loop-back test along the path to the target resource.


<strong style="color: blue;">HTTP Versions</strong>

So far there are three versions of HTTP. The first one was HTTP/0.9, which was truly primitive and never really specified in any standard. This was corrected by HTTP/1.0, which was issued as a standard in RFC 1945. (See references). HTTP/1.0 is the version of HTTP that is in common use today (usually with some 1.1 extensions), while HTTP/0.9 is rarely, if ever, used by browsers. (Some simpler HTTP clients still use it since they don't need the later extensions.)

RFC 2068 describes HTTP/1.1, which extends and improves HTTP/1.0 in a number of areas. Very few browsers support it (MSIE 4.0 is the only one known to the author), but servers are beginning to do so.

The major differences are a some extensions in HTTP/1.1 for authoring documents online via HTTP and a feature that lets clients request that the connection be kept open after a request so that it does not have to be reestablished for the next request. This can save some waiting and server load if several requests have to be issued quickly.

This document describes HTTP/1.0, except some sections that cover the HTTP/1.1 extensions. Those will be explicitly labeled.

\>\> <strong style="color:red;">The request send by the clients</strong>

<strong style="color:blue;">The shape of a request.</strong>

Basically, all requests look like this:

{% highlight http %}

[METH] [REQUEST-URI] HTTP/[VER]
[fieldname1]: [field-value1]
[fieldname2]: [field-value2]

[request body, if any]

{% endhighlight %}

The METH (for request method) gives the request method used, of which there are several, and which all do different things. The above example used GET, but below some more are explained. The REQUEST-URI is the identifier of the document on the server, such as "/index.html" or whatever. VER is the HTTP version, like in the response. The header fields are also the same as in the server response.

The request body is only used for requests that transfer data to the server, such as POST and PUT. (Described below.)

<strong style="color:blue;">getting document</strong>

There are several request types, with the most common one being GET. A GET request basically means "send me this document" and looks like this: "GET document_path HTTP/version". (Like it was described above.) For the URL "http://www.yahoo.com/" the document_path would be "/".

However, this first line is not the only thing a user agent (UA) usually sends, although it's the only thing that's really necessary. The UA can include a number of header fields in the request to give the server more information. These fields have the form "fieldname: value" and are all put on separate lines after the first request line.

Some of the header fields that can be used with GET are:

+ **User-Agent**: This is a string identifying the user agent. An English version of Netscape 4.03 running under Windows NT would send "Mozilla/4.03 [en] (WinNT; I ;Nav)". (Mozilla is the old name for Netscape. See the references for more details.)

+ **Referer**: The referer field (yes, it's misspelled in the standard) tells the server where the user came from, which is very useful for logging and keeping track of who links to ones pages.

+ **If-Modified-Since**: If a browser already has a version of the document in its cache it can include this field and set it to the time it retrieved that version. The server can then check if the document has been modified since the browser last downloaded it and send it again if necessary. The whole point is of course that if the document hasn't changed, then the server can just say so and save some waiting and network traffic.

+ **From**: This header field is a spammers dream come true: it is supposed to contain the email address of whoever controls the user agent. Very few, if any, browsers use it, partly because of the threat from spammers. However, web robots should use it, so that webmasters can contact the people responsible for the robot should it misbehave.

+ **Authorization**: This field can hold username and password if the document in question requires authorization to be accessed.

To put all these pieces together: this is a typical GET request, as issued by my browser (Opera):

{% highlight http %}

GET / HTTP/1.0
User-Agent: Mozilla/3.0 (compatible; Opera/3.0; Windows 95/NT4)
Accept: */*
Host: birk105.studby.uio.no:81

{% endhighlight %}

\>\> <strong style="color:red;">The response returned by server</strong>

<strong style="color: blue;">outline</strong>

What the server returns consists of a line with the status code, a list of header fields, a blank line and then the requested document, if it is returned at all. Sort of like this:

{% highlight http %}

HTTP/1.0 code text
Field1: Value1
Field2: Value2

...Document content here...

{% endhighlight %}

<strong style="color:blue;">the status code</strong>

The status codes are all three-digit numbers that are grouped by the first digit into 5 groups. The reason phrases given with the status codes below are just suggestions. Server can return any reason phrase they wish.

<strong>1xx: Informational</strong>

No 1xx status codes are defined, and they are reserved for experimental purposes only.

<strong>2xx: Successful</strong>

Means that the request was processed successfully.

200 OK
Means that the server did whatever the client wanted it to, and all is well.
Others
The rest of the 2xx status codes are mainly meant for script processing and are not often used.

<strong>3xx: Redirection</strong>

Means that the resource is somewhere else and that the client should try again at a new address.

- 301 Moved permanently

> The resource the client requested is somewhere else, and the client should go there to get it. Any links or other references to this resource should be updated.

- 302 Moved temporarily

> This means the same as the 301 response, but links should now not be updated, since the resource may be moved again in the future.

- 304 Not modified

> This response can be returned if the client used the if-modified-since header field and the resource has not been modified since the given time. Simply means that the cached version should be displayed for the user.

<strong>4xx: Client error</strong>

Means that the client screwed up somehow, usually by asking for something it should not have asked for.

- 400: Bad request

> The request sent by the client didn't have the correct syntax.

- 401: Unauthorized

> Means that the client is not allowed to access the resource. This may change if the client retries with an authorization header.

- 403: Forbidden

> The client is not allowed to access the resource and authorization will not help.

- 404: Not found

> Seen this one before? :) It means that the server has not heard of the resource and has no further clues as to what the client should do about it. In other words: dead link.

<strong>5xx: Server error</strong>

This means that the server screwed up or that it couldn't do as the client requested.

- 500: Internal server error

> Something went wrong inside the server.

- 501: Not implemented

> The request method is not supported by the server.

- 503: Service unavailable

> This sometimes happens if the server is too heavily loaded and cannot service the request. Usually, the solution is for the client to wait a while and try again.

<strong style="color: blue;">The response header fields</strong>

These are the header fields a server can return in response to a request.

- **Location**:
This tells the user agent where the resource it requested can be found. The value is just the URL of the new resource.
- **Server**:
This tells the user agent which web server is used. Nearly all web servers return this header, although some leave it out.
- **Content-length**:
This gives the size of the resource, in bytes.
- **Content-type**:
This describes the file format of the resource.
- **Content-encoding**:
This means that the resource has been coded in some way and must be decoded before use.
- **Expires**:
This field can be set for data that are updated at a known time (for instance if they are generated by a script). It is used to prevent browsers from caching the resource beyond the given date.
- **Last-modified**:
This tells the browser when the resource was last modified. Can be useful for mirroring, update notification etc.

 Happy coding.
