---
title: 9.2 Die Hard tribute 
author: Jake
layout: default
parent: Source
grand_parent: freebsd
permalink: /make-build-your-freebsd-word/freebsd-9-2-die-hard.html
categories:
  - Build Your FreeBSD World
tags:
  - beastie.4th
  - boot menu
  - die hard
  - freebsd 9.2
  - freebsd server
  - tribute
---
## FreeBSD 9.2 Die Hard tribute in beastie.4th
Love Die Hard? Check out FreeBSD 9.2 Die Hard tribute as a fun new beastie in the boot menu!

## The CEO Workstation - Nakatomi Socrates


![FreeBSD 9 Nakatomi Socrates](/wp-content/uploads/2013/09/freebsd9.png){:class="img-responsive"}
![FreeBSD 9.2 Nakatomi Socrates](/wp-content/uploads/2013/09/freebsd92.png){:class="img-responsive"}

<blockquote class="twitter-tweet">
  <p>
    <a href="https://twitter.com/search?q=%23BSD&src=hash">#BSD</a> 9.2 in Die Hard: <a href="http://t.co/vEzskl3f">http://t.co/vEzskl3f</a> - RT if you'd like to see <a href="https://twitter.com/search?q=%23FreeBSD&src=hash">#FreeBSD</a> 9.2 codenamed &#8220;Nakatomi Socrates&#8221; Dont be shy! /cc <a href="https://twitter.com/freebsd">@FreeBSD</a>
  </p>
  
  <p>
    — FreeBSD Help (@freebsdhelp) <a href="https://twitter.com/freebsdhelp/statuses/301598308760182784">February 13, 2013</a>
  </p>
</blockquote>

![Nakatomi socrates BSD 9.2 by AЯMEN, on Flickr"](https://farm4.staticflickr.com/3714/9516176127_c592724db5.jpg){:class="img-responsive"}

<a href="http://svnweb.freebsd.org/base/releng/9.2/sys/boot/forth/beastie.4th?revision=255027&view=co" target="_blank">http://svnweb.freebsd.org/base/releng/9.2/sys/boot/forth/beastie.4th?revision=255027&view=co</a>

```sh
: tribute-art ( x y -- )  see tribute[bw]-logo

	 Disable the brand art (we're going to use that space)
	s" set loader_brand=none" evaluate

	 Blank out the frame of the menu and move the title to left
	s" set loader_menu_title=" evaluate
	s" set loader_menu_frame=none" evaluate

	 Move the menu to the center of the screen
	s" set loader_menu_x=26" evaluate
	s" set loader_menu_y=12" evaluate
	s" set loader_menu_timeout_x=21" evaluate
	s" set loader_menu_timeout_y=24" evaluate

	2 - swap 39 - swap  top-left (see `fbsdbw-logo' comments above)

	2dup at-xy 11 spaces ." ,d      b." 1+
	2dup at-xy ."         ,d88]      [88b." 1+
	2dup at-xy ."      ,d888P" 34 emit ."  ,d88b. " 34 emit ." Y888b." 1+
	2dup at-xy ."  , &lt;888P" 34 emit ."  ,dP" 34 emit ." ,db." 34 emit
	           ." Yb. " 34 emit ." Y888&gt; ," 1+
	2dup at-xy ." &lt;88b.~ ,d888  " 34 emit ." YP" 34 emit
	           ."   888b. ~,d88&gt;" 1+
	2dup at-xy ."  ," 34 emit ." Y888888P" 34 emit ." ,db.,db." 34 emit
	           ." Y888888P" 34 emit ." ," 1+
	2dup at-xy ." &lt;88b." 34 emit ." YP" 34 emit ."  _ " 34 emit
	           ." YP" 34 emit 34 emit ." YP" 34 emit ."  _ " 34 emit
	           ." YP" 34 emit ." ,d88&gt;" 1+
	2dup at-xy ."   " 34 emit ." Y88b.,dP          Yb.,d88P" 34 emit 1+
	     at-xy ."      " 34 emit ." YP" 34 emit ."               " 34 emit
	           ." YP" 34 emit ." "
;

: tribute-text ( x y -- )  see tribute[bw]-logo

	swap 2 - swap  beastie adjustment (see `fbsdbw-logo' comments above)

	2dup at-xy ." CEO Workstation" 1+
	1+
	2dup at-xy ." Nakatomi Socrates FreeBSD 9.2" 1+
	2dup at-xy ." Z-Level Central Core" 1+
	1+
	     at-xy ." Preliminary Clearance Approved."
;

: tribute-logo ( x y -- )  color Socrates tribute (16 rows x 32 columns)

	 Produce the tribute art in bright green
	2dup at-xy ." [32;1m" 2dup tribute-art ." [37m"

	 Produce the tribute text in regular green
	2dup at-xy ." [32m" 2dup tribute-text ." [37m"

	 Distinguish the ``Free'' in tribute-text
	2 + swap 16 + swap at-xy ." Free"

 	 Put the cursor back at the bottom
 	0 25 at-xy
;

: tributebw-logo ( x y -- )  Socrates tribute (16 rows x 32 columns)

	 Produce the tribute art and text
	2dup tribute-art tribute-text

 	 Put the cursor back at the bottom
 	0 25 at-xy
;
```

Although, this probably wont be the default in 9.2 RELEASE, maybe this one will&#8230;  
![FreeBSD 9.2 Die Hard Nakatomi Socrates](/wp-content/uploads/2013/09/freebsd9-pre.png){:class="img-responsive"}
![FreeBSD 9.2 Die Hard Nakatomi Socrates](/wp-content/uploads/2013/09/freebsd92-pre.png){:class="img-responsive"}