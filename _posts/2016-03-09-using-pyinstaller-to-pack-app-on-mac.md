---
layout: post
title:  use pyinstaller pack app on mac
date:   2016-03-09 15:13:00 +0800
categories: articles
---

## introduction

PyInstaller reads a Python script written by you. It analyzes your code to
discover every other module and library your script needs in order to execute.
Then it collects copies of all those files -- including the active Python
interpreter! -- and puts them with your script in a single folder, or
optionally in a single executable file.

For the great majority of programs, this can be done with one short command,

> pyinstaller myscript.py

or with a few added options, for example a windowed application as a single-file
executable,

> pyinstaller --onefile --windowed myscript.py

You distribute the bundle as a folder or file to other people, and they can
execute your program. To your users, the app is self-contained. They do not
need to install any particular version of Python or any modules. They do not
need to have Python installed at all.

# supported package imports

except for the python egg package, the pyinstaller also support other common
used package. here is a [list](https://github.com/pyinstaller/pyinstaller/wiki/Supported-Packages).

if you have got into trouble with pack app with pyinstaller, you may first check
the list and compare with your dependency.

If your script requires files that PyInstaller does not know about, you must help it:

- You can give additional files on the pyinstaller command line.
- You can give additional import paths on the command line.
- You can edit the myscript.spec file that PyInstaller writes the first time you
  run it for your script. In the spec file you can tell PyInstaller about code
  modules that are unique to your script.
- You can write "hook" files that inform PyInstaller of hidden imports. If you
  create a "hook" for a package that other users might also use, you can
  contribute your hook file to PyInstaller.

PyInstaller does not include libraries that should exist in any installation of
this OS. For example in Linux, it does not bundle any file from /lib or /usr/lib,
assuming these will be found in every system.

## bundling to one folder

When you apply PyInstaller to myscript.py the default result is a single folder
named myscript.

advantages:

- easy to share with your client
- easy to update if you do not modify the dependency
- easy to debug when building, you can check the folder to find out what pyinstaller
  does.

disadvantages:

- destroy the program if a file is missing.

## how it works?

the program packed with the pyinstaller starts at the bootloader. this is the
heart of the myscript executable in the folder. this booloader is a binary
executable program works on major os. The bootloader creates a temporary Python
environment such that the Python interpreter will find all imported modules and
libraries in the myscript folder.

## bundling to one file

PyInstaller can bundle your script and all its dependencies into a single
executable named myscript (myscript.exe in Windows).

A disadvantage is that any related files such as a README must be distributed
separately. Also, the single executable is a little slower to start up than the
one-folder bundle.

Before you attempt to bundle to one file, make sure your app works correctly
when bundled to one folder. It is is much easier to diagnose problems in
one-folder mode.

## how on file program works?

The bootloader is the heart of the one-file bundle also.

1. create the temp dir in your system
2. extract the file into the temp dir
3. works like the one folder program

Because the program makes a temporary folder with a unique name, you can run
multiple copies of the app; they won't interfere with each other. However,
running multiple copies is expensive in disk space because nothing is shared.

## hide your source code

if you want to hiden your source code when compiling, just check the tutorial
[here](https://pythonhosted.org/PyInstaller/#Hiding the Source Code).

## usage

In the most simple case, set the current directory to the location of your
program myscript.py and execute:

> pyinstaller myscript.py

PyInstaller analyzes myscript.py and:

- Writes myscript.spec in the same folder as the script.
- Creates a folder build in the same folder as the script if it does not exist.
- Writes some log files and working files in the build folder.
- Creates a folder dist in the same folder as the script if it does not exist.
- Writes the myscript executable folder in the dist folder.
