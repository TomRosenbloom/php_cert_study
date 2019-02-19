# Getters and setters

The idea: set access level of properties to private and access them through getters and setters only, allowing you to validate or manipulate their values. Like this:

```php
class Table
{
    private $fields;
 
    public function __construct(array $fields) {
        $this->setFields($fields);
    }
    
    public function setFields(array $fields) {
        $this->fields = $fields;
    }
    
    public function getFields() {
        return $this->fields;
    }
}   
```

This is a standard pattern in OOP, so much so that most IDEs will generate basic getters and setters automatically from given properties. 

wrt setters, the main reason for doing this is to exert tight control on how properties are defined, for e.g. to enforce type, by preventing them being set outside of the setter. NB in PHP 7.4 you will be able to type hint properties, so you don't need to use a setter for that reason alone.

Also, if the business of setting/getting a property is at all complicated then it is obvs beneficial to move it out into a separate method.

## Type hinting properties

https://stackoverflow.com/a/52162072

The above includes the following code example which shows 4 ways you can (allegedly) control types of properties:

- Default values
- Decorators in comment blocks
- Default values in constructor
- Getters and setters

```php
class Foo
{
    /**
     * @var Bar
     */
    protected $bar = null;

    /** 
    * Foo constructor
    * @param Bar $bar
    **/
    public function __construct(Bar $bar = null){
        $this->bar = $bar;
    }

    /**
    * @return Bar
    */
    public function getBar() : ?Bar{
        return $this->bar;
    }

    /**
    * @param Bar $bar
    */
    public function setBar(Bar $bar) {
        $this->bar = $bar;
    }
}
```

I say allegedly because setting a default, for one, does not in any way prevent a property from being set to some other type outside the class, if the property is not private. Like this:

```php
class Person
{
    /**
     * @var integer
     */
    public $age = 0;
}

$person = new Person;
$person->age = 'wibble';
```

This won't raise any error, so actually the examples above look pretty erroneous.