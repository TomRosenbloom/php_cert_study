# Magic Methods

There are a number of magic methods of objects that are used under the hood so to speak but can be overwritten.

## \_\_get and \_\_set

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

## __clone

Used to create a copy of an object. In Singleton pattern it must be disabled by overriding it with a blank call: 

```
prinate __clone() {};
```



