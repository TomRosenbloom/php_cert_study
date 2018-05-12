

# Function overloading

## What it is

In certain other programming languages e.g. C, you can use a very direct form of 'function overloading', which is to say having multiple functions with the same name (a type of polymorphism). You can't do this in the same direct way in PHP because PHP won't allow you to re-declare a function.

So why on earth would you even want different functions to have the same name? I think this is something you do when the said functions perform the same task but in a different way according to the number/type of arguments. Two commonly given examples are function to (1) print value of a var, (2) function to calculate area of a shape - see below. 

You can do something like overloading in PHP - you can have a function that behaves differently according to the number and type of arguments. The way it is implemented isn't fantastically attractive. On the other hand, it seems like a bad idea to be able to have multiple same-named functions that do different things. Ok the idea is that they do the same thing in differing conditions, but this isn't enforced in any way (?). 

NB the reason for the difference is rigid v loose typing...

This is a pretty deep subject and I don't have time for it now. Suffice to say PHP supports a version of overloading and it can be implemented as per the examples below. You may need to use functions func_num_args(), func_get_arg($i), func_get_args() (when you do it in a procedural style). For PHP certification you need to know how these functions work hence Q6 to test whether you know the difference between func_num_args() and func_get_args().

[distinction between property overloading and method overloading https://medium.com/oceanize-geeks/phps-overloading-example-a432dd130169]

### Example 1 print value of var

(this is C, but v easy to follow)

```c
#include <iostream>
using namespace std;
 
void print(int i) {
  cout << " Here is int " << i << endl;
}
void print(double  f) {
  cout << " Here is float " << f << endl;
}
void print(char* c) {
  cout << " Here is char* " << c << endl;
}
 
int main() {
  print(10);
  print(10.10);
  print("ten");
  return 0;
}
```

The output:

```
Here is int 10 
Here is float 10.1 
Here is char* ten 
```

From: https://www.geeksforgeeks.org/function-overloading-c/

### Example 2: sum of n numbers

(procedural PHP)

```php
function findSum() {
    $sum = 0;
    foreach (func_get_args() as $arg) {
        $sum += $arg;
    }
    return $sum;
}

echo findSum(1, 2), '<br />'; //outputs 3
echo findSum(10, 2, 100), '<br />'; //outputs 112
echo findSum(10, 22, 0.5, 0.75, 12.50), '<br />'; //outputs 45.75
```

(OOP PHP)

```php
class Foo {

    public function __call($method, $args) {

        if ($method === 'findSum') {
            echo 'Sum is calculated to ' . $this->_getSum($args);
        } else {
            echo "Called method $method";
        }
    }

    private function _getSum($args) {
        $sum = 0;
        foreach ($args as $arg) {
            $sum += $arg;
        }
        return $sum;
    }

}

$foo = new Foo;
$foo->bar1(); // Called method bar1
$foo->bar2(); // Called method bar2
$foo->findSum(10, 50, 30); //Sum is calculated to 90
$foo->findSum(10.75, 101); //Sum is calculated to 111.75
```

From: https://softwareengineering.stackexchange.com/questions/165467/why-php-doesnt-support-function-overloading

### Example 3: calculate area of shape

```php
class TDshape {
const Pi = 3.142 ;  // constant value
 function __call($fname, $argument){
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

https://www.mindstick.com/Articles/12162/overloading-and-overriding-in-php

[1]: https://www.geeksforgeeks.org/function-overloading-c/
[2]: https://www.mindstick.com/Articles/12162/overloading-and-overriding-in-php	"Example 3"
[3]: https://softwareengineering.stackexchange.com/questions/165467/why-php-doesnt-support-function-overloading	"Example 2"

