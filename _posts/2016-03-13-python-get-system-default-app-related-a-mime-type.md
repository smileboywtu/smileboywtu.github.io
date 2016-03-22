---
layout: post
title:  get the default app related a file mime type using python
date:   2016-03-13 13:29:00 +0800
categories: articles
---

sometime you want to get the app list related to a file mime type.
for example, you design a desktop app and you want let the user set the
program to opening a file. so here you need to get all the programs which
can opening a kind of file. python is cross-platform language, so it's
very complex to do so. we start with a simple one:

Here we just support for

- windows
- linux
- mac

## open file using the default app

before you do so, you need to get the platform you use. there is a lib: **sys**.

{% highlight python %}

import sys
print sys.platform

{% endhighlight %}

you may use the code below to make your program cross-platform:

{% highlight python %}

import sys

if sys.platform.startswith('darwin'):
    print 'mac'
elif sys.platform.startswith('win32'):
    print 'windows'
elif sys.platform.startswith('linux'):
    print 'linux'
else:
    print 'other os.'

{% endhighlight %}

after you get the platform, you can google the app for opening a file using
command line how-to. after that you can use the python subprocess module to
invoke the system app to open the file.

Here is a example use the default system program to open the file:

there are two lib which is very popular to invoke system binary file:

- subprocess
- sh

the second is very easy to use, but it's only support linux and mac only. and
there is no hope support windows now, so here we can just use the subprocess.

{% highlight python %}

import sys
import subprocess

if sys.platform.startswith('darwin'):
    subprocess.call(['open', 'filename'])
elif sys.platform.startswith('win32'):
    subprocess.call(['cmd', '/c', 'start', 'filename'])
elif sys.platform.startswith('linux'):
    subprocess.call(['open', 'filename'])
else:
    print 'current no support for unknown os.'

{% endhighlight %}

there are many alternatives in linux using to open a file by default app.
you can use the **open --help** command to get more information.

## get installed binary list

this come to an not easy problem here, we want to let the user choose the
installed app to open a file. before that we should send the app list to the
user, but get the list and application path is not easy.

after search on googles long time, i just find how to figure out the app for
for open a type of file.

let's start with python source code file. **.py**, **.pyc**.

**1.** open the cmd

**2.** assoc .py or .pyc

**3.** copy the extention filename, here in my computer is: **Python.File**

**4.** open the regedit

**5.** search the file extention, here is **Python.File**

**6.** always under the **HKEY_CLASSES_ROOT** you can find the key.

**7.** go to shell/open/command, you will see the path for the app.

here we start to use python to open a program this way.

{% highlight python %}

import subprocess
from _winreg import OpenKey, EnumKey, EnumValue, HKEY_CLASSES_ROOT

process = subprocess.Popen(['cmd', '/c', 'assoc', '.py'],
                        stdout=subprocess.PIPE)
out, err = process.communicate()

file_ext = out.strip().split('=')[-1]

value = OpenKey(HKEY_CLASSES_ROOT, file_ext + '\shell\open\command')

path = EnumValue(value, 0)[1]

{% endhighlight %}

after you get the path, you now can get the app name from the path, then you
can start the app from the path.

here we just talk about the app for opening a file on windows, but others like
mac and linux, how to ?

here we just can open a file with the default app, but we can't totally find a
easy way to deal with multi-platform get all the 'open with list' now. if you
get a good idea, you can leave me a message.

Here is my way: I just use the default program to open the file and give the
user a list of current installed app on the platform or let the user choose
the path of the program.

> The information that is stored here makes sure that the correct program opens when you open a file by using Windows Explorer. This key is sometimes abbreviated as "HKCR." Starting with Windows 2000, this information is stored under both the HKEY_LOCAL_MACHINE and HKEY_CURRENT_USER keys. The HKEY_LOCAL_MACHINE\Software\Classes key contains default settings that can apply to all users on the local computer. The HKEY_CURRENT_USER\Software\Classes key contains settings that override the default settings and apply only to the interactive user. The HKEY_CLASSES_ROOT key provides a view of the registry that merges the information from these two sources.

on windows, I just find the way to get the open with list:

{% highlight shell %}

HKEY_CLASSES_ROOT\*

{% endhighlight %}

when you want to find all the program that can open **.mp3** file, you just find the
registry under this key, this key merges *HKEY_CURRENT_USER*, *HKEY_LOCAL_MACHINE*
so all the program that can open the program will all show here.

for example, if you want to find all the programs that can open the .mp3 file,
you just need to search all the class under HKEY_CLASSES_ROOT except .mp3 itself.
the others will be the programs which can open mp3 file. after you get the program
id, you can get the execute command. with that you can set the default open program
under **HKEY_CURRENT_USER/Software/Microsoft/Windows/CurrentVersion/Explorer/FileExt
/.mp3/UserChoice/Progid** or you can set it the default edit program under
**/HKEY_CURRENT_USER/Software/Classes/.mp3/shell/edit/command**

on mac you can read the **/Applications** for all the extentions inside .app
with it's **.plist** file.

{% highlight python %}

import plistlib
from pprint import pprint

r = plistlib.readPlist('file.plist')

for l in r['CFBundleDocumentTypes']:
    pprint(l['CFBundleTypeExtensions'])

{% endhighlight %}

now you can find all the application inside the /Applications and then get the
supported file extentions of them, and keep a copy of them inside the database.

on linux, you will find the ~/.local/share/applications and /usr/share/applications,
these two folder contains all the file type associations, just read the .list file inside
them, you can get all the program that can open the program.

Now I just try to write a library to get the open with programs cross-platform.
if you want to help, you can find the repository [here](https://github.com/smileboywtu/mimeopen).

## reference

1. [subprocess](https://docs.python.org/2/library/subprocess.html)
2. [sh](https://amoffat.github.io/sh/)
3. [invoke binary](http://stackoverflow.com/questions/434597/open-document-with-default-application-in-python)
4. [\_winreg](https://docs.python.org/2/library/_winreg.html)
5. [mimeopen](http://superuser.com/questions/572011/how-do-i-set-up-preferred-applications-in-nautilus-by-file-extension-rather-tha/573488#573488)
6. [perl-mimeopen](http://cpansearch.perl.org/src/PARDUS/File-MimeInfo-0.15/mimeopen)
7. [mac associations](http://apple.stackexchange.com/questions/64124/how-can-i-modify-the-list-of-applications-under-open-with)
8. [list installed app](http://www.howtogeek.com/165293/how-to-get-a-list-of-software-installed-on-your-pc-with-a-single-command/)
9. [window cmd list installed app](http://helpdeskgeek.com/how-to/generate-a-list-of-installed-programs-in-windows/)
10. [windows how to](http://superuser.com/questions/447277/list-all-installed-software-on-pc)
11. [open with list](http://stackoverflow.com/questions/3924753/where-does-windows-store-its-open-with-settings)
12. [mac application extentions](http://mac-how-to.wonderhowto.com/how-to/remove-duplicates-customize-open-with-menu-mac-os-x-0157100/)
