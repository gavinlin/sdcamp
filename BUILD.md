## Introduction ##

As open source books, ebooks and pdf format should be created on fly, the following sections describe those solution in detail.

 * contents/*.markdown : all source chapters
 * template/template.tex : book style
 * mmd2bok : script to generate latex from markdown and call `xelatex` to generate pdf

## Making Pdf books ##
PDF format is used to read/print in nice way like real book, [MultiMarkdown](http://fletcherpenney.net/multimarkdown/) is good at this and it is used instead to generate latex from multimarkdown, and latex tool `xelatex` (is part of [TexLive][texlive] now) is used to convert pdf from latex.

Please check [ctax](http://www.ctan.org/) and [TexLive][texlive] for more background for latex, which is quite complicated and elegant if you have never touched before.

### Ubuntu Platform ###

Ubuntu Platform Precise (12.04) is used mainly and it is continuously verified by travis-ci.org as well. 

Though texlive 2011 can be installed separately, the default one texlive 2009 from Ubuntu repository is good enough so far. 

    $ sudo apt-get install texlive-xetex
    $ sudo apt-get install texlive-latex-recommended # main packages
    $ sudo apt-get install texlive-latex-extra # package titlesec
	
You need to install related fonts for Chinese, fortunately they exist in ubuntu source also.
    
    $ sudo apt-get install ttf-arphic-gbsn00lp ttf-arphic-ukai # from arphic 
    $ sudo apt-get install ttf-wqy-microhei ttf-wqy-zenhei # from WenQuanYi


### Fedora Platform ###

Fedora 19 is verifed. 

Though texlive 2011 can be installed separately, the default one texlive 2009 from Ubuntu repository is good enough so far. 

    # yum install texlive-xetex
    # yum install texlive-latex
    # yum install texlive
    # yum install texlive-xetex-def texlive-xltxtra texlive-tocbibind
    # yum install texlive-euenc texlive-frame texlive-footnpag texlive-titlesec
    # yum install texlive-appendix
    
You need to install related fonts for Chinese, fortunately they exist in ubuntu source also.
    
    $ sudo apt-get install ttf-arphic-gbsn00lp ttf-arphic-ukai # from arphic 
    # yum install cjkuni-uming-fonts cjkuni-ukai-fonts
    # yum install wqy-microhei-fonts wqy-zenhei-fonts # from WenQuanYi
    

### Generate PDF ###

`multimarkdown` shall be compiled from git source.

Then it should work perfectly

	$ ./mmd2bok

### Latex template ###

the latex template is `template/template.tex`, it contains the style and include the `preface`,`chapters`,`appendix` latex files, which are generated from source chapters.

See `mmd2bok` for more information
    
[texlive]: http://www.tug.org/texlive/
