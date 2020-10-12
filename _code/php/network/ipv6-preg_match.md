---
id: 585
title: 'IPv6 Regexp'
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
# IPv6: Short IPv6 Regexp - PHP preg_match() example

#### IPv6 Regexp:

```re
'/^(((?=(?>.*?(::))(?!.+3)))3?|([dA-F]{1,4}(3|:(?!$)|$)|2))(?4){5}((?4){2}|(25[0-5]|(2[0-4]|1d|[1-9])?d)(.(?7)){3})z/i'
```

If you're interested in seeing some more IPv6 regex examples for other languages you should check this link [Shortest IPv6 Validation Regular Expression (RegEx)](http://home.deds.nl/~aeron/regex/).

**Here is the source, a better way of matching IPv6 using PHP preg_match()**

```php
function valid_ipv6_address( $ipv6 )
{
    $regex = '/^(((?=(?>.*?(::))(?!.+3)))3?|([dA-F]{1,4}(3|:(?!$)|$)|2))(?4){5}((?4){2}|(25[0-5]|(2[0-4]|1d|[1-9])?d)(.(?7)){3})z/i';
        if(!preg_match($regex, $ipv6))
        return (false); // is not a valid IPv6 Address

    return (true);
}
```