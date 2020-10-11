---
title: Human Readable Seconds
layout: default
parent: Date/Time
grand_parent: PHP
---
## Convert number of seconds to human readable string

```php
<?php

function secs_to_str($d)
{
       $periods = array(
              'day'    => 86400,
              'hour'   => 3600,
              'minute' => 60,
              'second' => 1
       );
       $parts = array();
       foreach ($periods as $name => $dur) {
              $div = floor($d / $dur);
              if ($div == 0) {
                     continue;
              } else if ($div == 1) {
                     $parts[] = $div . " " . $name;
              } else {
                     $parts[] = $div . " " . $name . "s";
              }
              $d %= $dur;
       }
       $last = array_pop($parts);
       if (empty($parts)) {
              return $last;
       } else {
              return join(', ', $parts) . " and " . $last;
       }
}
```

Usage:
```php
<?php

$start_time = time();

// put your code here
sleep(rand(1, 10)); // to give us some test data

$stop_time = time();
$seconds_taken = $stop_time - $start_time;

print(secs_to_str($seconds_taken));
```

Output:
```
9 seconds
2 hours, 8 minutes and 55 seconds
1 day, 10 hours, 30 minutes and 19 seconds
```
