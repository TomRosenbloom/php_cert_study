# Function handling functions

## call_user_func_array

This function is likely to be at the heart of an MVC application, as the final part of bootstrapping when the identified controller and method is called with the relevant parameters. It executes a user defined function with an array of parameters. This is how it is used:

```php
call_user_func_array(callable $callback, array $param_arr);
```

There's two important things that this allows you to do:

1. ### call functions dynamically 

   We can create some code that will call a function that will be determined only at run time i.e. the name of the function is extracted/created from some sort of (user) input . That is what happens in MVC where the name of the function comes from the URL (either directly, or via some form of route mapping).

   The function must be 'callable'. That means something that would pass the php is_callable() test - it must exist, but also be capable of being executed in the current context/scope.

2. ### have a variable number of parameters

   Since the 





[look into *function($foo) use ($bar){}*, also 'currying']