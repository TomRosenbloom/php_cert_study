# Scope resolution operator

I'm used to using the double colon as a means of executing static methods of a class, but there's rather more to it than that...

From the manual: "the double colon is a token that allows access to [static](http://be2.php.net/manual/en/language.oop5.static.php), [constant](http://be2.php.net/manual/en/language.oop5.constants.php), and overridden properties or methods of a class", or, from McArthur: "to access different levels of scope within classes".

The scope goes on the left hand side of the operator, the property or method on the right.

## Static properties

A **static property** (or 'member' ?) is a property that belongs to a class rather than a specific instance. So it is 'static' in the sense that it exists per class and will persist across instances of that class (but not in the sense that it doesn't change).

```php
class MyObject {
    public static $myStaticVar = 0;
    function myMethod() {
        self::$myStaticVar += 2;
        echo self::$myStaticVar . "\n";
    }
}
$instance1 = new MyObject();
$instance1->myMethod();
$instance2 = new MyObject();
$instance2->myMethod();
// output:
// 2
// 4
```

In this example (from the Kevin McArthur book) $myStaticVar is the same variable in both instances of MyObject. NB it must be accessed with the *self* scope identifier, otherwise you get an undefined variable notice (and no other output in this particular example). 

### *Self* scope

- *self* refers to the current class, and any instances of it
- *this* refers to the current instance only

The *self* scope includes all the classes in an inheritance tree:

```php
class MyObject {
    function myBaseMethod() {
        echo "I am declared in MyObject\n";
    }
}
class MyOtherObject extends MyObject {
    function myExtendedMethod() {
        echo "myExtendedMethod is declared in MyOtherObject\n";
        self::myBaseMethod();
    }
}
MyOtherObject::myExtendedMethod();
// output:
// myExtendedMethod is declared in MyOtherObject
// I am declared in MyObject
```

[aside: instead of writing explicitly 'MyOtherObject' etc. you can use \_\_METHOD\_\_ or \_\_FUNCTION\_\_ and \_\_CLASS\_\_ , which is worth noting though it makes this example less clear]

### *Parent* scope

So, what's *parent* for? It simply allows you to reference the original definition of a method that has been overridden in an extending class.

```php
class ExtendingClass extends BaseClass {
    function myMethod() {
        // new functionality
        parent::myMethod(); // defined in BaseClass
        // new functionality
    }
}
```

You would use this in a case where the extending class needs to define some additional functionality to an existing method.

[There's a lot of this kind of thing in Django, done in a somewhat different way, e.g. when you create a model, you extend the *model* base object. This base object includes amongst many others a *save* method which will do a default save action behind the scenes, but if you wanted to say manipulate some values before saving then you would use *def save(self, \*args, \*kwargs):* followed by additional functionality].

### Overriding static properties

A static variable (property) can be overridden in an extending class, and then both can be referenced in the extending class using parent::\$staticVar or self::\$staticVar. These can have different values. If the parent static property has been changed in the base class or some other extending class, then overriding it in the current class will create a new variable with a distinct value. Example from McArthur:

```php
class MyObject {
    public static $myStaticVar = 0;
    function myMethod() {
        self::$myStaticVar += 2;
        echo self::$myStaticVar . "\n";
    }
}
class MyOtherObject extends MyObject {
    public static $myStaticVar = 0;
    function myOtherMethod() {
        echo parent::$myStaticVar . "\n";
        echo self::$myStaticVar . "\n";
    }
}

$instance1 = new MyObject();
$instance1->myMethod();
$instance2 = new MyObject();
$instance2->myMethod();
$instance3 = new MyOtherObject();
$instance3->myOtherMethod();
// 2
// 4
// 4
// 0
```



### Directly access static properties

You can directly access a static variable without any object instantiation using *InstanceOfMyObject::$myStaticVar*. Note you can only do this from an instantiated object and not a class (unlike with static *methods* which can be called without an object being instantiated - see below). 

## Static methods

```php
class MyObject {
    public static function myMethod(){
        echo "foo";
    }
}
MyObject::myMethod();
// foo
```

Static methods work in a similar kind of way to static properties, though note you don't need to instantiate an object to directly access a static method of a class.

## Concrete example

A nice example of a concrete use for a static properties and methods comes from the Traversy MVC course. He defines a User class that has properties $name and $age, and also a static property $minPasswordLength (with a default value of 6). He also defines a static method to validate password length.  

```php
  class User {
    public $name;
    public $age;
    public static $minPassLength = 6;

    public static function validatePass($pass){
      if(strlen($pass) >= self::$minPassLength){
        return true;
      } else {
        return false;
      }
    }
  }

  $password = 'hello1';
  if(User::validatePass($password)){
    echo 'Password valid';
  } else {
    echo 'Password NOT valid';
  }
```

So static properties/methods are allowing you to create and use generalised properties about users that you can use without reference to a specific user object.

## Late static binding

There can be occasions where the *self* scope doesn't do quite what you would expect - see Example #1 in the manual. The introduction of the *static* scope fixes this - Example #2. This is 'late static binding'.

As applied to the example above under Overriding static properties, changing *parent* to *static* in MyOtherObject->MyOtherMethod will echo 0 instead of 4.

------

McArthur, Kevin. *Pro PHP - Patterns, Frameworks, Testing and More.* Apress, 2008  https://www.apress.com/gb/book/9781590598191