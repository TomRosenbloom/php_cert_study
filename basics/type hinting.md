# Type Hinting

As of PHP 5 you can tighten up your code with type hinting in function definitions. For example:

```php
function foo(string $bar){
    //
}
```

If you pass anything other than a string an error will be raised.

The type isn't restricted to predefined data types like string, array, object, etc. but can include user defined class names.

