## Spaceship operator

Spaceship operator is \<=\> (not to be confused with the not equal operator \<\>) and it tests the relative size of two operands. To be more accurate, it tests if the first operand is larger than the second.

If the first operand is larger, it evaluates to 1, if smaller -1, if equal, 0. 

So it's a comparison operator, and where for e.g. the greater than comparison operator > asks 'is this first operand greater than this second one', the spaceship operator asks 'is this first operand larger than this second one?'.

Examples:

| Operation                       | Value |
| ------------------------------- | ----- |
| 1 \<=\> 0, "b" <=> "a"          | 1     |
| 1 \<=\> 1                       | 0     |
| 1 \<=\> 2, 1 <=> 3, "a" <=> "b" | -1    |

The operator can also be used on arrays and objects, where it compares the total combined value of all the array members/object properties, I think. https://wiki.php.net/rfc/combined-comparison-operator Presumably this is also the case for all comparison operators - would be a good idea to look at the general case of array/object comparison.

NB the spaceship operator is common in other languages, but only added to PHP in version 7.

### Three way comparison

The proper name for what the spaceship operator does is 'three way comparison', that is it combines all three of A < B, A = B, or A > B in a single operation. 

By that token, the <=> operator can be used to make equivalents of any combinations of the more familiar comparison operators (or to put it another way, the existing operators are shorthand notation for uses of <=>). For example, A < B is equivalent to (A <=> B) === -1. 

### Use case

What is the use case for the spaceship operator? Primarily to make 'writing ordering callbacks for use with `usort()` easier.'

I think I have some notes on `usort()` elsewhere. It sorts an array using a user-defined comparison function. The <=> operator is useful within said user-defined function. 

`usort()` can be used like this:

```php
function order_func($a, $b)
{
    if ($a == $b) {
        return 0;
    }
    return ($a < $b) ? -1 : 1;
}

$a = array(3, 2, 5, 6, 1);

usort($a, "order_func");
```

The user-defined value comparison function must return 'an integer less than, equal to, or greater than zero if the first argument is considered to be respectively less than, equal to, or greater than the second'. So in practice that's going to be -1, 0 or 1.

So `usort()` does some hidden magic, going through the array and using the return values from all the necessary comparisons of array values to put them in order. What those 'necessary comparisons' are will depend on what the underlying sorting algorithm is (quicksort is what PHP uses).

https://www.php.net/manual/en/function.usort.php#108116 - some more detail about the inner workings of `usort()`. Useful to know for more complex comparisons. Essentially, 'If you return -1 that moves the $b variable down the array, return 1 moves $b up the array and return 0 keeps $b in the same place.'

Returning to the example above, to re-write the comparison function using the spaceship operator:

```php
function order_func($a, $b) {
    return $a <=> $b;
}
```

...and, probably more to the point, it makes sorting on multiple columns mushc easier:

```php
function order_func($a, $b) {
    return [$a->x, $a->y, $a->foo] <=> [$b->x, $b->y, $b->foo];
}
```

