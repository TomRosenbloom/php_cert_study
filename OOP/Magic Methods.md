# Magic Methods

There are a number of magic methods of objects that are used under the hood so to speak but can be overwritten.

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
    class dog {
        public $Name;

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

Because there is no *meow* method, call is called. If the meow method is uncommented __call will be ignored and meow will be called.

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