---
title: IPv4 address check private (RFC 1918)
layout: default
parent: Network
grand_parent: PHP
---
## IPv4 address check private (RFC 1918)

#### 1. ip2long()
```php
function ip_is_private($ip) {
        $pri_addrs = array(
                          '10.0.0.0|10.255.255.255',
                          '172.16.0.0|172.31.255.255',
                          '192.168.0.0|192.168.255.255',
                          '169.254.0.0|169.254.255.255',
                          '127.0.0.0|127.255.255.255'
                         );

        $long_ip = ip2long($ip);
        if($long_ip != -1) {

            foreach($pri_addrs AS $pri_addr) {
                list($start, $end) = explode('|', $pri_addr);

                 // if is private
                 if($long_ip >= ip2long($start) && $long_ip <= ip2long($end))
                 return (TRUE);
            }
    }

return (FALSE);
}
```

Example of this function being used
```php
$ip_address1 = "10.4.200.1";
$ip_address2 = "192.168.0.2";
$ip_address3 = "213.224.100.43";

if ( ip_is_private ( $ip_address1 ) ) {
    printf ( "%s is from a private address rangen", $ip_address1 );
} else {
    printf ( "%s is *NOT* from a private address rangen", $ip_address1 );
}

if ( ip_is_private ( $ip_address2 ) ) {
    printf ( "%s is from a private address rangen", $ip_address2 );
} else {
    printf ( "%s is *NOT* from a private address rangen", $ip_address2 );
}

if ( ip_is_private ( $ip_address3 ) ) {
    printf ( "%s is from a private address rangen", $ip_address3 );
} else {
    printf ( "%s is *NOT* from a private address rangen", $ip_address3 );
}
```

Which gives us this output
```
10.4.200.1 is from a private address range
192.168.0.2 is from a private address range
213.224.100.43 is *NOT* from a private address range
```