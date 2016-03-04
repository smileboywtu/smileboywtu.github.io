---
layout: post
title:  merge serval pdfs and add watermark
date:   2016-03-04 18:31:00 +0800
categories: articles
---

my first work in company is to download serval pdfs and merge into, then add
the watermark to the pdf.

here I use the python 2.7 and PyPDF2 library.

install the dependency:

+ sudo apt-get install python2.7
+ pip install PyPDF2

merge two pdf:

{% highlight python %}

pdf_merger = PdfFileMerger()

current_slice = PdfFileReader('test.pdf')
pdf_merger.append(current_slice)

pdf_merger.write('output.pdf')

{% endhighlight %}

add the watermark to the pdf:

add the watermake to pdf is the same as merging two pdfs, you should first make
a pdf as your watermark and then merge the first page of the watermark pdf to
each page of the target pdf.

{% highlight python %}

watermark_pdf = canvas.Canvas(watermark_path)
watermark_pdf.setFont(DEFAULT_FONT, 20)
watermark_pdf.rotate(30)
watermark_pdf.setFillColorRGB(0, 0, 0, 0.2)
watermark_pdf.drawString(0 * inch, x * inch, "water mark")
watermark_pdf.showPage()
watermark_pdf.save()

{% endhighlight %}

then merge the water with the target pdf which you want to add watermark to.

Happy coding all done.
