---
id: 585
title: 'IPv6: Short IPv6 Regexp &#8211; PHP preg_match() example'
date: 2012-02-11T14:23:28+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=585
permalink: /coding-snipits/php-regex-ipv6-with-preg_match-revisited.html
parent: Network
grand_parent: PHP
categories:
  - Coding
  - PHP Programming
tags:
  - ipv6
  - ipv6 regex
  - pcre
  - php examples
  - programming
  - regex
---
Back in July 2011 I posted an example IPv6 regular expression using PCRE via PHP&#8217;s preg_match() function to check for a valid IPv6 address. Find it [here](http://www.mebsd.com/coding-snipits/php-regex-ipv6-with-preg_match.html "PHP Regex IPv6 with preg_match"). My example was long, and had some issues. It didn&#8217;t match short hand IPv6 addresses properly and it was not very elegant. Recently a visitor commented on this regex and suggested a much better way of doing it.

#### IPv6 Regexp:

<pre class="console-text notranslate">'/^(((?=(?>.*?(::))(?!.+3)))3?|([dA-F]{1,4}(3|:(?!$)|$)|2))(?4){5}((?4){2}|(25[0-5]|(2[0-4]|1d|[1-9])?d)(.(?7)){3})z/i'
</pre>

If you&#8217;re interested in seeing some more IPv6 regex examples for other languages you should check this link <a title="Shortest IPv6 Validation Regular Expression (RegEx)" href="http://home.deds.nl/~aeron/regex/" target="_blank">Shortest IPv6 Validation Regular Expression (RegEx)</a>.

Here is the updated version, I have not found any bugs yet so please feel free to test it below or try it out in your own code. Let us know how you get on.

<p style="color: #0A0; font-size: 14pt;">

**Congratulations!** \&#8217;%s\&#8217; is a valid IPv6 address!

&#8216;, $address );  
}  
else  
{  
printf( &#8216;

<p style="color: #D00; font-size: 14pt;">
  Sorry&#8230; \&#8217;%s\&#8217; is <strong>not</strong> a valid IPv6 address&#8230;
</p>

&#8216;, $address );  
}  
}  
?>

<div style="clear: both;">
  <strong>Example IPv6 addresses</strong></p> 
  
  <ul>
    <li>
      2001:2d12:c4fe:5afe::1
    </li>
    <li>
      2a00:1450:400c:c01::68
    </li>
    <li>
      ::1
    </li>
  </ul>
</div>

**Here is the source, a better way of matching IPv6 using PHP preg_match()**

<pre class="notranslate prettyprint" style="margin-bottom: 30px;">function valid_ipv6_address( $ipv6 )
{
    $regex = '/^(((?=(?>.*?(::))(?!.+3)))3?|([dA-F]{1,4}(3|:(?!$)|$)|2))(?4){5}((?4){2}|(25[0-5]|(2[0-4]|1d|[1-9])?d)(.(?7)){3})z/i';
        if(!preg_match($regex, $ipv6))
        return (false); // is not a valid IPv6 Address

    return (true);
}
</pre>