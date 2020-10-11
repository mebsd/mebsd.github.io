---
title: MySQL fetch multi-dimensional array
layout: default
parent: MySQL
grand_parent: PHP
---

## MySQL fetch all rows into multi-dimensional array
 
It's not always desirable to be working inside a `while( $row = mysql_fetch_assoc($qur))` when you want to do stuff with a table full of data. What can be more flexible is to load the rows into memory as an array in PHP so we can come back to them, re order, filter, modify or what ever you like.

One word of warning however, if you are going to be load alot of rows you could run into problems with the amount of memory your PHP code uses, or the time it takes to run. So use this when you feel it's appropriate.

```php
<?php

function mysql_fetch_rowsarr($result, $numass = MYSQL_BOTH)
{
       $got = array();
       mysql_data_seek($result, 0);
       while ($row = mysql_fetch_array($result, $numass)) {
              array_push($got, $row);
       }
       return $got;
}
```

Now after you have preformed the `mysql_query()` function instead of passing it into a while loop with `mysql_fetch_array()` or similar you can use this function and get returned a multi-dimensional array.

Example MySQL table
```sql
mysql> select * from `my_table`;
+----+------------+-------------------+--------------+
| id | film       | staring           | release_date |
+----+------------+-------------------+--------------+
|  1 | Monsters   | Scoot McNairy     | 2010-10-29   |
|  2 | Saw 3D     | Tobin Bell        | 2010-10-29   |
|  3 | Due Date   | Robert Downey Jr. | 2010-11-05   |
|  4 | Jackass 3D | Johnny Knoxville  | 2010-11-05   |
+----+------------+-------------------+--------------+
4 rows in set (0.00 sec)
```

To return this table as an array
```php
<?php

$qur = mysql_query("SELECT * FROM `my_table`");
$sql_array = mysql_fetch_rowsarr($qur);

print_r($sql_array);
```

Giving us the following array
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

If you want to return an array with only associative keys call the function with this additional arg
`$sql_array = mysql_fetch_rowsarr( $qur, MYSQL_ASSOC );`
