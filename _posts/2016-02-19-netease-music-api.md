---
layout: post
title:  netease music api usage
date:   2016-02-19 12:28:00 +0800
categories: articles
---

netease music is the most popular between young people. it use the machine
learning technology to recommend music to the user. this brings much exciting
feeling than any other music box.

this post teach you how to use the netease music web api. you can find the api
source file [here]['api'].

to use the api separately you need to install:

+ python 2.7
+ requests
+ BeautifulSoup4
+ pycrypto

I recommend you use the **ipython** to test this api.

**1.** start ipython

**2.** prepare the username and password, you can use phone to login

{% highlight python %}
    username = 'your_user_name'
    import hashlib
    password = hashlib.md5('your_password').hexdigest()
{% endhighlight %}

**3.** login to the netease music

{% highlight python %}
    from api import NetEase
    instance = NetEase()
    login_info = instance.login(username, password)
{% endhighlight %}

**4.** get the userid from response and request the user playlist

{% highlight python %}
    user_id = login_info['account']['id']
    favorites = instance.user_playlist(user_id)
    # convert to list
    top_list = instance.dig_info(favorites, "top_playlists")
{% endhighlight %}

**5.** get the sublist of the top_list

{% highlight python %}
    user_list_id = top_list[0]['playlist_id']
    # use list id to get more detail
    songs = instance.playlist_detail(user_list_id)
    song_list = instance.dig_info(songs, "songs")
    print song_list
{% endhighlight %}

**6.** get the list

{% highlight python %}
[
    ...

    {'album_name': u"Ich hab' Dich lieb",
    'artist': u'Schnuffel',
    'mp3_url': 'http://m2.music.126.net/dMyZL7vNBvzhrPYFuOHI2A==/1241348627769500.mp3',
    'quality': 'LD 96k',
    'song_id': 19098254,
    'song_name': u'H\xe4schenparty'},
    {'album_name': u'\u7231\u7684\u4e3b\u9898\u66f2 \u53f0\u6e7e\u7248',
    'artist': u'\u7fa4\u661f',
    'mp3_url': 'http://m2.music.126.net/2nYy3sLgrYphuK7YoPBsJg==/1214960348701276.mp3',
    'quality': 'HD 320k',
    'song_id': 4874797,
    'song_name': u'Hear Me Cry'}]

{% endhighlight %}

This is very useful if you want to integrate the netease music into your own app
or website, you can use this one.

Happy coding.


['api']: https://github.com/darknessomi/musicbox/blob/master/NEMbox/api.py
