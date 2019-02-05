# Autoloading

In general terms, autoloading is the use of some mechanism to load classes that we might need to use but without having to load each one (or any of them) specifically. 

In PHP, way back I used __autoload() in a simplistic way like this:

```php
function __autoload($class_name) {
	include "classes/$class_name.php";
}
```

This code is in, say, autoload.php, and the first thing you do in the app is include (or require) autoload.php. Once that is done, any existing class can be called. \__autoload is a ‘magic method’ i.e. called automatically (once defined). So in PHP when you instantiate an object from a class, if PHP doesn’t find the class in the current script, it will automatically call __autoload.

Note __autoload is now deprecated in favour of spl_autoload_register. You give to spl_autoload_register the name of your autoloader function. So it is doing what the name states, registering your autoload function. 

Here's an example of how that might be used (this is from Traversy 'Object Oriented PHP & MVC'. He puts this in the bootstrap file). Note here he doesn't separately create an autoloader function and then register it, but supplies an anonymous function. Hence this isn't perhaps the best example, but anyway...

```php
spl_autoload_register(function($className){
    require_once 'libraries/' . $className . '.php';
});
```



The advantage of spl_autoload_register is you can register multiple autoloaders. You can call it more than once, and/or you can feed it an array. (Achieved via the SPL 'autoload queue' - If you remember, SPL is ‘Standard PHP Library’, a “collection of interfaces and classes that are meant to solve standard problems” written by some clever bod (Marcus Boerger) and introduced with PHP 5. It existed before PHP 5 as a separate library, but in PHP 5 it was brought into the PHP core distribution). 

Why is this important? Because in any complicated framework type scenario (i.e. any modern PHP application) you will inevitably need to register more than one autoloader, and you will not want to worry about possible conflicts with calls to __autoload in any third party libraries.

A side note, for comparison: in Python autoloading is specifically eschewed, and regarded as undesirable. 

> Parts of Python's philosophy (aka "Python's Zen") are "explicit is better than implicit", "Readability counts", and "In the face of ambiguity, refuse the temptation to guess" - which makes a "feature" like PHP's "autoload" highly unpythonic, as 1. it's way less explicit than a "import mymodule" or "from mymodule import SomeName", 2. it's also way less readable (you don't know clearly where a name is imported from), and 3. when two or more modules define a same name (which is perfectly valid and quite common in Python), it would have to try and guess which one you want (which would be technically impossible). 

<https://stackoverflow.com/questions/32068957/python-modules-autoloader>

When I think about it, autoloading is very PHP – it’s kind of convenient but also slightly dubious [?]. It’s the sort of thing that makes PHP unpopular. But NB PHP uses namespaces to deal with the issue of class name conflicts. Further note that namespacing is compelled in the latest standard of autoloading in PHP (PSR-4). More on that later.

## Autoloading with Composer

https://phpenthusiast.com/blog/how-to-autoload-with-composer

If you're using Composer then it makes sense to use the Composer autoloader for your own code. There's a few different ways to use it. What they have in common is you manually edit the composer.json file to add your own classes, which then makes them available throughout your code base without using includes/requires.

### Classmap

```json
{
  "autoload": {
    "classmap": [
      "path/to/FirstClass.php",
      "path/to/directory"
    ]
  }
}
```

This is the crudest way of doing it. It's easy to understand, and it doesn't require the use of namespaces. However, it doesn't offer that great an advantage over a long list of includes. 

#### An aside re file and class names:

It's conventional to define one class per file, and for the name of the file to be the same as the name of the class, but that is just a convention, not a requirement. Normally you might say have a folder app/core/helpers, and in that folder a file UrlHelper.php containing a single class UrlHelper containing a variety of (static) methods for common url-related tasks, for example redirect($path).  Provided you have included app/core/helpers in your classmap (or autoloaded the class in some other way) you can call it with *UrlHelper::redirect($path)*. But NB this will work equally well if the file is called something else e.g. foobar.php, as long as it is in the app/core/helpers folder. 

If you want to access redirect($path) directly, without the class name prefix, well you can't. If the function/method is defined within a class, you can never access it directly. Whatever way you include the class - whether it be with a manual include/require or via autoloading - it makes no difference. Essentially manual inclusion and autoloading are doing the same thing, which is bringing together separate bits of code into a single larger piece of code. So even if you copied the class definition into the same php file where you want to access redirect() you will still have to use the class name prefix. However, you can use **namespacing** and the *use* keyword to assign an alias to the function, something like this:

```php
namespace Baz;

use function \Baz\redirect as redirect;
```

 ...but that tends to lead into a nightmare of namespacing issues (classes in the global namespace will become unavailable), of which more later I think...

### PSR-4

PSR-4 is the current best standard for autoloading, and 'it compels us to use namespaces'.

```json
"autoload": {
    "psr-4": {
        "App\\": "app/"
    }
}
```
Now if I put *namespace App;* above the class definition for UrlHelpers, in order to use a url helper such as redirect in a script I must add *use App\UrlHelper* (and I can alias that with *as foo* if it makes things clearer). I still can't use redirect() without a prefix (I think: I tried again with *use function as*, but couldn't get that to work)

The benefits are:

- the **use** keyword at the top of the script makes it very clear what classes are being used
- aliasing can be used
- you can assign any class to a particular namespace
- you don't have to specify every class location
- autoloading done per call, rather than everything up front (?)
- don't have to *dump composer autoload* for every change

