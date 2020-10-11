---
title: IPv6 Regex
layout: default
parent: Network
grand_parent: PHP
---

## IPv6 Regex with preg_match()
```php
function valid_ipv6_address( $ipv6 ) 
{
        $pattern1 = '([A-Fa-f0-9]{1,4}:){7}[A-Fa-f0-9]{1,4}';
        $pattern2 = '[A-Fa-f0-9]{1,4}::([A-Fa-f0-9]{1,4}:){0,5}[A-Fa-f0-9]{1,4}';
        $pattern3 = '([A-Fa-f0-9]{1,4}:){2}:([A-Fa-f0-9]{1,4}:){0,4}[A-Fa-f0-9]{1,4}';
        $pattern4 = '([A-Fa-f0-9]{1,4}:){3}:([A-Fa-f0-9]{1,4}:){0,3}[A-Fa-f0-9]{1,4}';
        $pattern5 = '([A-Fa-f0-9]{1,4}:){4}:([A-Fa-f0-9]{1,4}:){0,2}[A-Fa-f0-9]{1,4}';
        $pattern6 = '([A-Fa-f0-9]{1,4}:){5}:([A-Fa-f0-9]{1,4}:){0,1}[A-Fa-f0-9]{1,4}';
        $pattern7 = '([A-Fa-f0-9]{1,4}:){6}:[A-Fa-f0-9]{1,4}';

        $full = "/^($pattern1)$|^($pattern2)$|^($pattern3)$|^($pattern4)$|^($pattern5)$|^($pattern6)$|^($pattern7)$/";

        if (!preg_match($full, $ipv6)) {
            return false; // is not a valid IPv6 Address
        }

    return true;
}
```

Basic example usage of this function
```php
$addr6 = "2001:2d12:c4fe:5afe::1";
if (valid_ipv6_address($addr6)) {
    printf("%s is a valid IPv6 Address!n", $addr6);
} else {
    printf("%s is *NOT* a valid IPv6 Address!n", $addr6);
}

$addr6 = "4d4x:3daz:rdc2:23sd::1";
if (valid_ipv6_address($addr6)) {
    printf("%s is a valid IPv6 Address!n", $addr6);
} else {
    printf("%s is *NOT* a valid IPv6 Address!n", $addr6);
}
```

Gives us this output
```
2001:2d12:c4fe:5afe::1 is a valid IPv6 Address!
4d4x:3daz:rdc2:23sd::1 is *NOT* a valid IPv6 Address!
```