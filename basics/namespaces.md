# Namespaces

Conceptually, namespacing for classes is *a bit* like scope for variables, or *a bit* like a directory structure for files, in that it allows different things with the same (local) names to co-exist. Having said that, the way variable scope and file systems work is entirely different from namespaces, so maybe not such a great analogy. Scope is a very fundamental part of a language whereas namespacing is something built on top. Scope and directories are essentially hierarchical, but namespaces are not - you can declare a namespace anywhere and use it from anywhere, and you can declare classes into a namespace in more than one place. [NB namespaces can contain not just classes but interfaces, functions and constants].

This is how you define a namespace:

```php
namespace MyNamespace;
```

The declaration must be the very first thing in the script (including non-php code).

...and this is how you use a namespace (in some other script):

```php
use MyNamespace;
```

*but no - this will always produce a warning (?) - see below...

You can declare sub-namespaces:

```
namespace MyProject\Sub\Namespace\
```

You can declare multiple namespaces in a single file, but you shouldn't.

Let's say we have this file structure:

```
home
|
+-- test.php
|   
+--foo
   |
   +-- bar.php
```

...then define class wibble in bar.php:

```php
class wibble
{
    public function __construct()
    {
        echo "wibble";
    }
}
```

~~To access this class in test.php without using namespacing we would need to include bar.php.~~

```php
include('foo\bar.php');

$test = new wibble;
```

...and we'd need to do that for every instance where we want to use a bit of code that's not in the current file. Well in fact we wouldn't, because we'd use autoloading of some sort, but that's kind of irrelevant - regardless of whether we include the external code manually or with autoloading, what we are effectively doing is combining all these disparate bits of code into one big bit of code. In that scenario we are liable to encounter name collisions, and all the more so when we are including code from third parties. It's this problem that namespacing is intended to deal with.

In summary of the above, it's easy to get confused between **use** and **include**, especially if you find yourself in a codebase where some autoloading is going on behind the scenes that you're not fully aware of.

I think this is the key to understanding namespacing. Through the use of includes and autoloading, any codebase is effectively one huge single piece of code, including not just your own local code and that of your current and historical colleagues, but the code of every third party package, library, and framework that you use. Given that case it's easy to see how likely there are to be obscure problems caused by naming conflicts. Namespacing exists to deal with this inevitable problem by separating the codebase into discrete and self-contained units.

Where people get confused is that it may *seem* that **use** works like **include** in some circumstances, because a 'class not found' error was fixed (or caused) by fiddling with **use** or **namespace**. So, returning to the example I started above, all of that will work fine, but if I add a namespace declaration e.g. 'namespace Baz;' to the top of bar.php, then test.php will *not* find class *wibble*. It worked without a namespace declaration because with no namespace declarations everything is in the global namespace, and it doesn't work with a namespace declaration because now I've put *wibble* in a namespace that test.php isn't being told about. If I want to persist in using a namespace, then I have to add **use** to test.php (or in fact I could add 'namespace Baz;').

Where this gets a little more confusing is if I simply add 'use Baz;' to test.php I'll get a warning 'The use statement with non-compound name 'Baz' has no effect' (but wibble will still execute). In fact any use of use with a non-compound name 'has no effect'. NB this is proved because $test = new Baz\Wibble; will still work.

Wow, this is confusing! Here is a summary things that do/don't work. Given this file foo\bar.php (as above):

```php
namespace Baz;

class Wibble
{
    public function __construct()
    {
        echo "wibble";
    }
}
```

...this is what will/won't work in index.php (or any other file):

```php
namespace Baz; // this would obviate any use of 'use', but doesn't allow aliasing

include('foo\bar.php'); // always necessary, unless there is some autoloading going on

// these two lines work:
use Baz\Wibble as Wibble;
$test = new Wibble;

// as do these:
use \Baz as Baz;
$test = new Baz\Wibble;

// but...
use Baz; // warning, The use statement with non-compound name 'Baz' has no effect
$test = new Baz\Wibble; // but this still works
$test = new Wibble; // whereas this doesn't

// this will work in any circumstance i.e. with no 'namespace' or 'use'
$test = new Baz\Wibble;
```

Note: sometimes you might see a standard php class or function preceded with a forward slash, like $date = new \DateTime(). That is I believe just a precaution to ensure that it is the global version that is being used and not some local version with the same name. It doesn't mean - I don't think - that once you declare a local namespace you won't be able to use the standard functions *without* the forward slash. In fact it *does* mean that, but only for classes and functions that were added after namespacing was introduced in PHP 5.3. So, for e.g. something like strpos will always work anywhere without a forward slash, but this will generate an error:

```php
namespace Baz;
$a = new ArrayObject;
```

 ...unless we put a forward slash before ArrayObject. [Note the Exception class is post-5.3]

NB there can be valid reasons for defining a local version of a standard post-5.3 PHP class, which is 'overloading' of classes/functions - something found more commonly in other languages e.g. C. Overloading is 'the ability to create multiple functions of the same name with different implementations'. I've come across this idea before, but not sure why you would want to do it... Wikipedia: "Calls to an overloaded function will run a specific implementation of that function appropriate to the context of the call, allowing one function call to perform different tasks depending on context."

## Unqualified/qualified/ fully qualified names

If you want to use a class, function or constant from your current namespace, you can use an unqualified name - same as you would if you were not using a namespace (which in fact means you are in the global namespace).

If you want to access something in a namespace that is a sub-namespace of the current namespace, you use a qualified name, as with $test = new Baz\Wibble in the example above.

Finally, if you put a backslash at the front like $test = new \Baz\Wibble that makes the name fully qualified, i.e. relative to the global namespace. This gives you access to namespaces above your current place in the namespace hierarchy.

