Globals are evil

https://stackoverflow.com/a/5166527

```php
function fn()
{
    global $foo;              // never ever use that
    $a = SOME_CONSTANT        // do not use that
    $b = Foo::SOME_CONSTANT;  // do not use that unless self::
    $c = $GLOBALS['foo'];     // incl. any other superglobal ($_GET, …)
    $d = Foo::bar();          // any static call, incl. Singletons and Registries
}
```