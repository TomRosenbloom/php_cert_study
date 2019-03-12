# Value Object

An address, for example, is a value object. An address has some obvious properties like first line, town, post code etc. It is the values of these properties that define an address. If we defined two address objects with identical properties, they are the same address. 

A person, for example, is an entity object, not a value object. We might define a person as having a single property, name (if that's all our system requires). But two persons with the same are not the same person.

NB the address example could be simplified. We don't really need the separate properties. We could just say address = '10 Downing Street', i.e. just a string. (Then if we have two instances of this address where the spelling is different, we have an error. So, by the bye, the DeVA data verification exercise was an exercise in turning entity objects into value objects!)

> A Value Object has no identifier attached to it; only the combination of the values of its properties gives it any identification. http://paul-m-jones.com/archives/6400

Value objects are immutable.

## Comparing objects in PHP

PHP doesn't have a concept of value objects, i.e. you can't define an object as specifically a value object. (But you can in other languages?)

There is nothing to prevent you separately instantiating two objects that ought to be value objects. When you do that, they will be equal but not identical.

```php
$address1 = new Address('Main street 42');
$address2 = new Address('Main street 42');

$address1 == $address2;  // true     equal...
$address1 === $address2;  // false   ...but not identical
```

https://stackoverflow.com/questions/9834843/what-is-an-example-of-a-value-object-in-php

Actually it's not strictly accurate to say PHP doesn't have a concept of value objects. You can't define your own, but some built in objects are value objects e.g. ArrayObject and Date classes.