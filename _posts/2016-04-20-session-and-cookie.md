---
layout: post
title:  session and cookie
date:   2016-04-20 10:02:00 +0800
categories: articles
---

## What is a "Cookie"?

A cookie is a small piece of text stored on a user's computer by their browser.
Common uses for cookies are authentication, storing of site preferences, shopping
cart items, and server session identification.

Each time the users' web browser interacts with a web server it will pass the
cookie information to the web server. Only the cookies stored by the browser that
relate to the domain in the requested URL will be sent to the server. This means
that cookies that relate to www.example.com will not be sent to www.exampledomain.com.

In essence, a cookie is a great way of linking one page to the next for a user's
interaction with a web site or web application.

## What is a "Session"?

A session can be defined as a server-side storage of information that is desired
to persist throughout the user's interaction with the web site or web application.

Instead of storing large and constantly changing information via cookies in the
user's browser, only a unique identifier is stored on the client side (called a
"session id"). This session id is passed to the web server every time the browser
makes an HTTP request (ie a page link or AJAX request). The web application
pairs this session id with it's internal database and retrieves the stored variables
for use by the requested page.

## Setting and reading cookies

Using the [cookie_set] method we can set cookies to store information for use in
later pages. The following code shows how easy it is to store a user's details
such as their name and email address which they may have entered on a "Contact
Us" form. This would then allow later pages to pre-populate forms with this information.

{% highlight js %}
Cookie_Set(
    'UserDetails'='John Doe|john.doe@example.com',
    -Domain='example.com',
    -Expires='1440',
    -Path='/'
)
{% endhighlight %}

In this example the cookie named "UserDetails" contains the user name and email
address delimited by a "pipe" character. This can be read and interpreted, then
output in the following code.

{% highlight js %}
local( userDetails = decode_url(cookie('UserDetails'))->split('|'))

if(#userDetails->size)=>{^
    'User Name = '+#userDetails->get(1)
    '<br />'
    'Email Address = '+#userDetails->get(2)
^}
{% endhighlight %}

## Using Sessions

To store information that is not appropriate to store client-side, we use sessions.
Lasso has built in session handling, and deals with the setting and retrieval of
the cookie itself. It will automatically set and retrieve the session id, which
is the only thing stored client-side.

To set up a new session, we first start the session, then add to it the variables
we would like to store in it. Those variables are stored within Lasso's session database.

{% highlight js %}
// Start the session.
session_start(
	'mySessionName',
	-expires = 1440,
	-usecookie=true
)

// Add variables to the session
if(session_result('mySessionName') != 'load') => {
	session_addVar('mySessionName', 'sv_userId')
	session_addVar('mySessionName', 'sv_userName')
	session_addVar('mySessionName', 'sv_userEmail')
	session_addVar('mySessionName', 'sv_favouriteColour')
}

!var_defined('sv_userId') ? 	var('sv_userId' = integer)
!var_defined('sv_userName') ? 	var('sv_userName' = string)
!var_defined('sv_userEmail') ? 	var('sv_userEmail' = string)
!var_defined('sv_favouriteColour') ? 	var('sv_favouriteColour' = 'red')

{% endhighlight %}

## Reference

[LassoSoft](http://www.lassosoft.com/Tutorial-Understanding-Cookies-and-Sessions)
