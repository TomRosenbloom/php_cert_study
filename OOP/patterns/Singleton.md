# Singleton

## Purpose and use

A singleton class is a class designed in such a way as to ensure that only one instance of it can possibly exist at any one time. So it is used to create something that you only want one of, like a database connection. In that case it wouldn't necessarily hurt to have more than one connection, it's just a waste of resources. Singleton is more importantly used to manage the state of an application in an otherwise stateless environment.

## Implementation

Crucial components:

- private constructor
- static variable to hold unique instance of the class
- public static method to access the instance

Here's an example of using Singleton to create a database connection (from Kevin McArthur book).

```php
class Database {
    private $_db;
    static $_instance;
    
    private function __construct() {
        $this->_db = pg_connect('dbname=exampledb');
    }
    
    private __clone() {};
    
    public static function getInstance() {
        if(!(self::$_instance instanceof self)){
            self::$_instance = new self();
        }
        return self::$_instance;
    }
    
    public function query($sql) {
        return pg_query($this->_db, $sql);
    }
}

$db = Database::getInstance();
$db->query('SELECT * FROM table');
```

So this an object of this class can never be instantiated in the normal way. $foo = new Database() will never work because it won't be able to call the private constructor. The only way to instantiate an object of this class is to call the static getInstance method with \$db = Database::getInstance(). The getInstance method simply inspects the static var \$\_instance and checks whether or not it currently contains an instance of the class. If so, it returns the instance, if not, it first instantiates an object and then returns it.

NB the line *__clone() {};* is a PHP specific workaround to disable the __clone magic method which could otherwise be used to defeat the singleton pattern.

## When not to use it

In some cases a static class is more appropriate than a Singleton. Example:

```php
class SomeClass {
    private function __construct() {}
    
    public static function someMethod() {
        //
    }
}

$result = SomeClass::someMethod();
```

So this I guess would be used to create some utility function (or a collection of related functions?) that can be called statically i.e. without unnecessarily instantiating a pointless object. The blank constructor explicitly prevents any object being instantiated.

## Singleton controversy

Considered an anti-pattern by some, but regardless is used a lot in MVC frameworks.

What's the problem with it? Mainly, but not solely because it can be used to create quasi-globals. Globals are bad because they create a hidden dependency.

I think the truth is they have their place - difficult to avoid in a sensible way in fact - but can be abused.

