---
title: Recursive in_array()
layout: default
parent: PHP
---
## Recursive in_array()

```php
<?php

function recursive_in_array($needle, $haystack)
{
       foreach ($haystack as $stalk) {
              if (
                     $needle == $stalk ||
                     (is_array($stalk) && recursive_in_array($needle, $stalk))
              ) {
                     return true;
              }
       }
       return false;
}
```

Search the following array for the release date `2010-11-05`

```php
Array
(
    [0] => Array
        (
            [0] => 1
            [id] => 1
            [1] => Monsters
            [film] => Monsters
            [2] => Scoot McNairy
            [staring] => Scoot McNairy
            [3] => 2010-10-29
            [release_date] => 2010-10-29
        )

    [1] => Array
        (
            [0] => 2
            [id] => 2
            [1] => Saw 3D
            [film] => Saw 3D
            [2] => Tobin Bell
            [staring] => Tobin Bell
            [3] => 2010-10-29
            [release_date] => 2010-10-29
        )

    [2] => Array
        (
            [0] => 3
            [id] => 3
            [1] => Due Date
            [film] => Due Date
            [2] => Robert Downey Jr.
            [staring] => Robert Downey Jr.
            [3] => 2010-11-05
            [release_date] => 2010-11-05
        )

    [3] => Array
        (
            [0] => 4
            [id] => 4
            [1] => Jackass 3D
            [film] => Jackass 3D
            [2] => Johnny Knoxville
            [staring] => Johnny Knoxville
            [3] => 2010-11-05
            [release_date] => 2010-11-05
        )

)
```

```php
<?php

if (recursive_in_array("2010-11-05", $sql_array)) {
       print("Release date '2010-11-05' found!");
}
```