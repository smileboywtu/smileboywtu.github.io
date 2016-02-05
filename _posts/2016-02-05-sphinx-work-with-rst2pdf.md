---
layout: post
title:  sphinx work with rst2pdf
date:   2016-02-05 17:09:00 +0800
categories: articles
---

there are many online good book that is open source using sphinx to generate
html, the book source file is saved on github. when you want to read the book
offline it's a good idea to make the rst file to be pdf document.

here I find many good books on a github [respository][1]. many of them are open
source and give the source rst file.

so today I'd like to tell you how to convert them to pdf.

first you'd have the pre-require tools installed:

+ python2.7
+ sphinx
+ rst2pdf

here after install the python2.7, just install the other two use pip:

+ pip install sphinx
+ pip install rst2pdf

Let's start to create the pdf:

**1.** download or clone the book source respository
{% highlight bash %}
git clone 'http://github.com/example/'
{% endhighlight %}

**2.** then find the conf.py inside the source directory.
add the *rst2pdf.pdfbuilder* to extensions:

{% highlight python %}
extensions = ['sphinx.ext.autodoc','rst2pdf.pdfbuilder']
{% endhighlight %}

there are list to config the pdf, you can add to the conf.py:

{% highlight python %}

# -- Options for PDF output --------------------------------------------------

# Grouping the document tree into PDF files. List of tuples
# (source start file, target name, title, author, options).
#
# If there is more than one author, separate them with \\.
# For example: r'Guido van Rossum\\Fred L. Drake, Jr., editor'
#
# The options element is a dictionary that lets you override
# this config per-document.
# For example,
# ('index', u'MyProject', u'My Project', u'Author Name',
#  dict(pdf_compressed = True))
# would mean that specific document would be compressed
# regardless of the global pdf_compressed setting.

pdf_documents = [
    ('index', u'MyProject', u'My Project', u'Author Name'),
]

# A comma-separated list of custom stylesheets. Example:
pdf_stylesheets = ['sphinx','kerning','a4']

# A list of folders to search for stylesheets. Example:
pdf_style_path = ['.', '_styles']

# Create a compressed PDF
# Use True/False or 1/0
# Example: compressed=True
#pdf_compressed = False

# A colon-separated list of folders to search for fonts. Example:
# pdf_font_path = ['/usr/share/fonts', '/usr/share/texmf-dist/fonts/']

# Language to be used for hyphenation support
#pdf_language = "en_US"

# Mode for literal blocks wider than the frame. Can be
# overflow, shrink or truncate
#pdf_fit_mode = "shrink"

# Section level that forces a break page.
# For example: 1 means top-level sections start in a new page
# 0 means disabled
#pdf_break_level = 0

# When a section starts in a new page, force it to be 'even', 'odd',
# or just use 'any'
#pdf_breakside = 'any'

# Insert footnotes where they are defined instead of
# at the end.
#pdf_inline_footnotes = True

# verbosity level. 0 1 or 2
#pdf_verbosity = 0

# If false, no index is generated.
#pdf_use_index = True

# If false, no modindex is generated.
#pdf_use_modindex = True

# If false, no coverpage is generated.
#pdf_use_coverpage = True

# Name of the cover page template to use
#pdf_cover_template = 'sphinxcover.tmpl'

# Documents to append as an appendix to all manuals.
#pdf_appendices = []

# Enable experimental feature to split table cells. Use it
# if you get "DelayedTable too big" errors
#pdf_splittables = False

# Set the default DPI for images
#pdf_default_dpi = 72

# Enable rst2pdf extension modules (default is only vectorpdf)
# you need vectorpdf if you want to use sphinx's graphviz support
#pdf_extensions = ['vectorpdf']

# Page template name for "regular" pages
#pdf_page_template = 'cutePage'

# Show Table Of Contents at the beginning?
#pdf_use_toc = True

# How many levels deep should the table of contents be?
pdf_toc_depth = 9999

# Add section number to section references
pdf_use_numbered_links = False

# Background images fitting mode
pdf_fit_background_mode = 'scale'

{% endhighlight %}

**3.** build the pdf files

{% highlight bash %}
sphinx-build -b pdf source _build/pdf
{% endhighlight %}

make sure you are in the right directory.

that's done, happy reading.


[1]:https://github.com/vhf/free-programming-books
