# Introduction #

As open source books, ebooks and pdf format should be created on fly, the following sections describe those solution in detail.

The solution below is based on [Pro Git][progit]; while it is little updated on format inside. 

# Making Pdf books #
PDF format is used to read/print in nice way like real book, [pandoc][pandoc] good at this and it is used instead to generate latex from markdown, and latex tool `xelatex` (is part of [TexLive][texlive] now) is used to convert pdf from latex.

Please check [ctax](http://www.ctan.org/) and [TexLive][texlive] for more background for latex, which is quite complicated and elegant if you have never touched before.

## Ubuntu Platform ##

Ubuntu Platform Oneiric (11.10) is used mainly due to pandoc. 

[pandoc][pandoc] can be installed directly from source, which version is 1.8.x. If you use Ubuntu 11.04, then it is just 1.5.x.

Though texlive 2011 can be installed separately, the default one texlive 2009 from Ubuntu repository is good enough so far. 

	$ sudo apt-get install pandoc
    $ sudo apt-get install texlive-xetex
    $ sudo apt-get install texlive-latex-recommended # main packages
    $ sudo apt-get install texlive-latex-extra # package titlesec
	
You need to install related fonts for Chinese, fortunately they exist in ubuntu source also.
    
    $ sudo apt-get install ttf-arphic-gbsn00lp ttf-arphic-ukai # from arphic 
	$ sudo apt-get install ttf-wqy-microhei ttf-wqy-zenhei # from WenQuanYi

Then it should work perfectly

	$ ./makepdfs zh
    
Just remind you, some [extra pandoc markdown format](http://johnmacfarlane.net/pandoc/README.html) is used inside this book:

  * code syntax highlight (doesn't work in pdf, while it should work in html/epub which needed later)
  * footnote
    
# Making Ebooks #

I start to use pandoc for epub and html as well, since from 1.8.x, pandoc supports them as well.

You can simple run the command below to generate related ebooks

    $ ./makeebooks zh  # default for html
	$ FORMAT=epub ./makeebooks zh  # for epub
    
# Handle PDF #

~~~~~~~~~~~~~ {.bash}
pdftk sdcamp.zh.pdf dump_data > in.info
pdfbokmark.rb --input in.info > pdfmarks # may update pdfmarks for broken pages
pdftk A=book-cover.pdf B=sdcamp.zh.pdf cat A3-4 B3-end A7 output merged.pdf
gs -dBATCH -dNOPAUSE -sDEVICE=pdfwrite -sOutputFile=sdcamp.zh.community.book.pdf merged.pdf pdfmarks    
~~~~~~~~~~~~~~~~
	
[pandoc]: http://johnmacfarlane.net/pandoc/    
[progit]: http://github.com/progit/progit 
[texlive]: http://www.tug.org/texlive/
