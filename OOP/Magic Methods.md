# Magic Methods

There are some 'magic' methods pre-defined in PHP. What makes them magic? Primarily that they are invoked automatically in some given situation. Secondly that they can be - and usually are - extended with your own functionality.

The names of PHP's magic methods begin with a double underscore. That's just a convention to prevent name collisions.

So if you compare magic methods with standard PHP functions, the difference is that the standard function (1) must be called explicitly (2) is a black box with a set functionality. Some non-magic functions can or must take other functions as arguments so to that extent their behaviour can be set by the coder, and in that sense they are not exactly a black box, but that's different to what magic methods do. Magic methods are more like a request to the coder to supply some local code to be executed in some particular circumstance. 

A further difference is that magic methods are *methods* i.e. they are designed to be used in OOP in the context of a class and not as part of a procedural block. Having said that, it is possible to use at least some magic methods procedurally, for example \_\_autoload().

## \_\_construct

The most commonly used magic method is \_\_construct(), which of course is invoked automatically when an object is instantiated.   

## \_\_get and \_\_set

Used for 'property overloading'. 

The __get method of a class will be executed if the property is not accessible or does not exist. 

Example from book:

```php
class BankBalance {
	private $balance;
	public function __get($propertyName) {
		echo "No property $propertyName";
	}
    public function __set($propetyName, $value) {
        echo "Cannot set $propertyName to $value";
    }

}
$myAccount = new BankBalance();
$myAccount->balance = 100; // Cannot set balance to 100
echo $myAccount->nonExistingProperty; // No property nonExistingProperty
```

With a bit of tweaking this example becomes more instructive:

```php
class BankBalance {
	//private $balance; // same outcome whether or not this commented out
	public function __get($propertyName) {
		return $this->balance;
	}
    public function __set($propertyName, $value) {
        //echo "Cannot set $propertyName to $value";
        $this->$propertyName = $value;
    }

}
$myAccount = new BankBalance();
$myAccount->balance = 100;
echo $myAccount->nonExistingProperty; // 100
```

So you can use \_\_get and \_\_set as a magical alternative to explicit getters and setters, but this is v bad coding practice (and most IDEs will autogenerate getters and setters for you anyway).

## __clone

Used to create a copy of an object. In Singleton pattern it must be disabled by overriding it with a blank call: 

```
private __clone() {};
```



## __call

The __call method of a class will be executed if the called method does not exist. 

Here's an easy to understand though purposeless example (from http://www.hackingwithphp.com/6/14/4/):

```php
class dog 
{
    public function bark() {
        print "Woof!\n";
    }

    // public function meow() {
    // print "Dogs don't meow!\n";
    // }

    public function __call($function, $args) {
        $args = implode(', ', $args);
        print "Call to $function() with args '$args' failed!\n";
    }
}

$poppy = new dog;
$poppy->meow("foo", "bar", "baz");
```

Because there is no *meow* method, \_\_call is called. If the meow method is uncommented __call will be ignored and meow will be called.

The practical use of __call seems to be limited to doing a PHP version of function overloading:

```php
class TDshape {
const Pi = 3.142 ;  // constant value
 function __call($name, $argument){
    if($name == 'area')
        switch(count($argument)){
            case 0 : return 0 ;
            case 1 : return self::Pi * $argument[0] ; // 3.14 * 5
            case 2 : return $argument[0] * $argument[1];  // 5 * 10
        }

    }

}
$circle = new TDshape();
echo "Area of circle:".$circle->area(5)."</br>"; // display the area of circle
 $rect = new TDshape();
echo "Area of rectangle:".$rect->area(5,10); // display area of rectangle
```

However, (1) it's debatable whether this is really overloading as it is understood in other languages (2) use of \_\_call (and \_\_get and \_\_set) is generally regarded as bad practice.

Let me have a stab at explaining for myself the difference between PHP 'overloading' and the understanding of that term in other languages. In this PHP example, and others to be found online, we have a single function that will behave in different ways according to what arguments are supplied. You can in fact do that in a non-OOP way using if statements to test the arguments and branching the function's behaviour accordingly (that seems like a horrible thing to do!). The use of \_\_call() in the above example seems kind of hacky. I'd be interested to know if there is any legitimate reason to use this kind of thing [interesting suggestion in comments on the PHP manual page is to use \_\_set() to prevent the creation of  properties not defined in the class http://php.net/manual/en/language.oop5.overloading.php#119898].

From the PHP manual:

> Overloading in PHP provides means to dynamically create properties and methods.

and:

> PHP's interpretation of overloading is different than most object oriented languages. Overloading traditionally provides the ability to have multiple methods with the same name but different quantities and types of arguments.

Remember from above that PHP also implements 'property overloading' with \_\_get() and \_\_set(), \_\_call() is the same deal but for methods.

In other languages, and I think crucially *compiled* and also statically-typed languages, overloading means something different. Essentially, I think, you can define more than one function with the same name, but they become distinct functions by virtue of having different numbers of or types of arguments. So the definition of the function consists of not just the name but name plus arguments. It's obvious why this can't work in a dynamically typed language like PHP. Note the connection between static-typed/compiled and dynamic-typed/interpreted languages (right?).

The wikipedia example of overloading uses the example of a function called Print which will be a different implementation according to the type of argument, say an image, or some text - that makes sense. Also functions for calculating volume of different shapes. You can see the value of this - if you had a stream of mixed up items that need to be printed or mixed up shapes that need volumes calculating.

From here: https://www.techopedia.com/definition/3236/overloading for e.g. it seems like the PHP idea of function overloading is more like *virtual functions*. True overloading seems to be possible in a compiled and statically-typed language.

Bit more clarity here https://softwareengineering.stackexchange.com/questions/332214/can-an-interpreted-statically-typed-language-be-considered-type-safe: there is no such distinction as interpreted v compiled languages. In fact any language can be compiled then run (including PHP), or run interpreted. In fact compilation and interpretation are just different ways of executing code: https://softwareengineering.stackexchange.com/questions/262275/can-every-language-be-compiled-and-can-every-language-be-interpreted. Compilation means turning one language into another, interpretation means executing on the fly. But it's not even that simple really. Compilation v interpretation is a pretty deep subject in fact, and I'm going to stop with it here for now. 

However static/dynamic typing *is* an inherent property of a given language [maybe]. Note that strong v weak typing is an entirely separate thing. Python is strongly typed, and if you try to add an integer to a string it will throw an error. PHP on the other and is weakly typed and adding an integer and a string will work in some way (there being rules about what type to go with in what situation).

> Python is dynamic typed and strong typed; Java is static typed and strong typed; PHP is dynamic typed and weak typed; C is static typed and weak typed

https://www.sitepoint.com/typing-versus-dynamic-typing/

This has been a big digression and should be moved into a new doc...

## \_\_toString()

This is just a shortcut method that allows you to have a way of providing a string representation of an object [Python does something like this a bit more natively iirc - ?].

```php
class Penguin {

  public function __construct($name) {
      $this->species = 'Penguin';
      $this->name = $name;
  }

  public function __toString() {
      return $this->name . " (" . $this->species . ")\n";
  }
}

$tux = new Penguin('tux');
echo $tux;
```

https://lornajane.net/posts/2012/9-magic-methods-in-php

## More

http://php.net/manual/en/language.oop5.magic.php