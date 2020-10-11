---
title: Recursive unlink()
layout: default
parent: PHP
---
## Recursive rm

```php
<?php

function recursive_rm($path)
{
       // open directory handle - return on fail
       if (!$dir = @opendir($path)) {
              return false;
       }

       // for each file in directory
       while (($file = readdir($dir)) !== false) {
              // if not special
              if ($file == '.' || $file == '..') {
                     continue;
              }

              // if cant delete path send path back round
              if (!@unlink($path . "/" . $file)) {
                     recursive_rm($path . "/" . $file);
              }
       }

       // close directory handle
       closedir($dir);

       // delete base directory
       @rmdir($path);

       return true;
}
```

Example usage:
```php
<?php

recursive_rm("/path/to/delete");
```