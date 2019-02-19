# Type Hinting

As of PHP 5 you can tighten up your code with type hinting in function definitions. For example:

```php
function foo(string $bar){
    //
}
```

If you pass anything other than a string an error will be raised.

In PHP 5 type hinting was limited, perhaps surprisingly, to array, and specific object classes, and *didn't* include scalar types int, float, bool, string. 

Type hinting reduces the scope for unexpected behaviour caused by wrong types being used, and by same token saves you the aggro of constantly using functions like is_numeric, is_integer etc. etc.

