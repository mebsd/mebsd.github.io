---
title: MySQLi – Return MySQL row as an Object
layout: default
parent: MySQL
grand_parent: PHP
permalink: /coding-snipits/php-mysqli-return-mysql-row-as-an-object.html
categories:
  - Coding
  - PHP Programming
tags:
  - connection error
  - database check
  - error messages
  - log messages
  - mysql password
  - Objects in PHP
  - PHP and Syslogd
  - PHP object class
  - private function
---
After playing with OOP in PHP for a few days, and deciding it's something I want to use more, I put together a class to help with all the extra stuff that needs to be done to return a MySQL row to PHP as an object. This class uses the PHP MySQLi class to keep us totally in OOP land.

In addition to using MySQLi, this class also logs debug or error messages to syslogd, using the standard syslog log alert levels. You can see more debug output in syslog by raising this number or using one of the system defines, e.g. `LOG_NOTICE` (see $config in the `get_book_obj` class below). You may want to check `/var/log/messages` to see the output, or setup a separate log for it in `/etc/syslog.conf`.

OK, to kick off, here is the MySQLi db class.

```php
<?php

    class db
    {
        private $db, $config;
        private $last_query;

        public function __construct ( $config )
        {
            $this->config = $config;
            $this->db_conn();
        }

        private function debug_log( $msg, $log_level = 0 )
        {
            $time = date("r");

            // also send to syslog daemon
            if ( $this->config['SYSLOG'] >= $log_level )
            {
                openlog  ( "php-db-obj", LOG_PID, LOG_DAEMON );
                syslog   ( $log_level, $msg );
                closelog ( );
            }
        }

        public function db_conn ()
        {
            /* do mysqli connection and init class */
            $this->db = @new mysqli ( $this->config['MYSQL']['SERVER'],
                                      $this->config['MYSQL']['USERNAME'],
                                      $this->config['MYSQL']['PASSWORD'],
                                      $this->config['MYSQL']['DATABASE'] );

            // Check for connection error
            // use mysqli_connect_errno() function not $db->connect_error
            // for compatibility with php vesion < 5.2.9
            if ( mysqli_connect_errno() )
            {
                $this->debug_log ( 'Connection Error (' . mysqli_connect_errno() . ') ' . mysqli_connect_error(), LOG_EMERG );
                die ();
            }
            else
                $this->debug_log ( 'Database connection success!', LOG_NOTICE );
        }

       public function query ( $sql )
        {
            $this->last_query = $sql;

            $this->debug_log ( sprintf ( "SQL query executed: [ %s ]", $this->last_query ), LOG_NOTICE );

            $result = $this->db->query ( $sql );
            $this->confirm_query ( $result );

        return ( $result );
        }

        private function confirm_query ( $result )
        {
            if ( !$result )
            {
                $this->debug_log ( sprintf ( "Last SQL query: [ %s ]", $this->last_query ), LOG_ERR );
                $this->debug_log ( "Database query failed: " . $this->db->error, LOG_NOTICE );
                die ();
            }
        }

        public function db_close ()
        {
            $this->debug_log ( "Cleaning Database connection", LOG_NOTICE );

            if ( isset ( $this->db ) )
            {
                 $this->db->close();
                 unset ( $this->db );
            }
        }
    }
```

Now we have our db class we need to point some code at it, so we can actually get back a row from MySQL. To demonstrate this I will be using another class, which will get a book as an object from our made up online book shop.

I have used a number of static methods and variables for this one, but of course you can do it however you like. :)

```php
class get_book_obj
    {
        private $db;
        private static $mysql_host = "localhost";
        private static $mysql_user = "mysql_user";
        private static $mysql_pass = "mysql_pass";
        private static $mysql_db   = "bookshop";

        public $id;
        public $title;
        public $author;
        public $no_pages;
        public $hardback;
        public $price;
        public $isbn;

        // generic sql qurey function
        public static function sql ( $sql = "" )
        {
            $config = array ( "MYSQL" => array ( "SERVER"   => self::$mysql_host,
                                                 "USERNAME" => self::$mysql_user,
                                                 "PASSWORD" => self::$mysql_pass,
                                                 "DATABASE" => self::$mysql_db ),
                              "SYSLOG" => LOG_NOTICE );

            $db = new db( $config );

            $result_set = $db->query ( $sql );
            $object_array = array ();

            while ( $row = $result_set->fetch_object () )
            {
                $object_array[] = self::instantiate ( $row );
            }
            $db->db_close();

        return ( $object_array );
        }

        // instantiate device object
        private static function instantiate ( $record )
        {
            $object = new self;

            foreach ( $record AS $attribute => $value )
            {
                if( $object->has_attribute ( $attribute ) )
                {
                    $object->$attribute = $value;
                }
            }

        return ( $object );
        }

        // build device attributes
        private function has_attribute ( $attribute )
        {
            $object_vars = get_object_vars ( $this );

        return ( array_key_exists ( $attribute, $object_vars ) );
        }
    }
```

Now we have both these classes we can easily get our book object, below is the MySQL table we will be selecting a book from.

```
mysql> select * from books;
+----+-----------------+--------------+----------+----------+-------+-------------------+
| id | title           | author       | no_pages | hardback | price | isbn              |
+----+-----------------+--------------+----------+----------+-------+-------------------+
|  1 | Alone In Berlin | Hans Fallada |      588 | N        |  9.99 | 978-0-141-18938-3 |
+----+-----------------+--------------+----------+----------+-------+-------------------+
1 row in set (0.01 sec)
```

The object that will be returned will be that of a book, with attributes of title, author, price, etc. To get this object we would do the following

```php
$obj = get_book_obj::sql( "SELECT * FROM `books` WHERE `id` = 1" );
    var_dump( $obj );
```

`$obj` becomes the object and var_dump describes it to use as such

```php
array(1) {
  [0]=>
  object(get_book_obj)#5 (8) {
    ["db":"get_book_obj":private]=>
    NULL
    ["id"]=>
    string(1) "1"
    ["title"]=>
    string(15) "Alone In Berlin"
    ["author"]=>
    string(12) "Hans Fallada"
    ["no_pages"]=>
    string(3) "588"
    ["hardback"]=>
    string(1) "N"
    ["price"]=>
    string(4) "9.99"
    ["isbn"]=>
    string(17) "978-0-141-18938-3"
  }
}
```

Each attribute we want to be returned in the object needs to be defined at the top of the class and is checked by the `has_attribute()` method. This is also how, in this example, we can stop some attributes from being put into out object.

For example, changing

```php
class get_book_obj
    {
        private $db;
        private static $mysql_host = "localhost";
        private static $mysql_user = "mysql_user";
        private static $mysql_pass = "mysql_pass";
        private static $mysql_db   = "bookshop";

        public $id;
        public $title;
        public $author;
        public $no_pages;
        public $hardback;
        public $price;
        public $isbn;
...
```

to&#8230;

```php
class get_book_obj
    {
        private $db;
        private static $mysql_host = "localhost";
        private static $mysql_user = "mysql_user";
        private static $mysql_pass = "mysql_pass";
        private static $mysql_db   = "bookshop";

        public $id;
        public $title;
        public $author;
        public $isbn;
...
```

Will omit the attributes no_pages, hardback and price, returning the object

```php
array(1) {
  [0]=>
  object(get_book_obj)#5 (5) {
    ["db":"get_book_obj":private]=>
    NULL
    ["id"]=>
    string(1) "1"
    ["title"]=>
    string(15) "Alone In Berlin"
    ["author"]=>
    string(12) "Hans Fallada"
    ["isbn"]=>
    string(17) "978-0-141-18938-3"
  }
}
```

This, of course, could be done in a much more generic and flexible way with some extra time and coding.

I hope someones find this useful on their journey to learning and understanding OOP in PHP and just OOP in general. Any comments, queries or suggestions are welcome, as always.