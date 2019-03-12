# Function handling functions

## call_user_func_array

This function is likely to be at the heart of an MVC application, as the final part of bootstrapping when the identified controller and method is called with the relevant parameters. It executes a user defined function with an array of parameters. This is how it is used:

```php
call_user_func_array(callable $callback, array $param_arr);
```

What this allows you to do is call functions dynamically.

We can create some code that will call a function that will be determined only at run time i.e. the name of the function is extracted/created from some sort of (user) input . That is what happens in MVC where the name of the function comes from the URL (either directly, or via some form of route mapping).

The function must be 'callable'. That means something that would pass the php is_callable() test - it must exist, but also be capable of being executed in the current context/scope.

Note that call_user_func_array further allows you to call a function that is defined as a method of a class - instead of passing a string (the function name) as first parameter, you pass an array of two strings, the names of the class and method respectively).

The arguments are supplied via the second parameter, an array. Obviously that's a required mechanism because in order to call functions dynamically we have to be able to vary the number of arguments, because different functions have different numbers of parameters. Note that if you supply the wrong number of arguments for the function that you call, an error will be raised - unless there are *more* arguments than specified in the function definition, in which case the extra ones will just be ignored. 

### Example

```php
class EventController
{
    static function eventsInRange($id, $order, $postcode, $range)
    {
        echo "List events within $range miles of postccode $postcode";
        echo " ordered by $order and highlighting event with id $id";
    }
    
    static function eventsOnDate($date)
    {
        echo "List events on date $date";
    }
    
}

call_user_func_array(['EventController','eventsInRange'], ['1', 'date', 'BS5 6EP', '10']);
// List events within 10 miles of postccode BS5 6EP ordered by date and highlighting event with id 1

$controller = 'EventController';
$action = 'eventsOnDate';
$params = ['10-1-2020'];
call_user_func_array([$controller, $action],$params);
// List events on date 10-1-2020

```

NB you get a E_DEPRECATED warning if the functions are not defined as static.

This example shows the kind of way call_user_func_array might be used in an MVC system.  

### Calling inbuilt PHP functions dynamically

NB the callable supplied to call_user_func can be a php function. 

```php
$foo = 'wibble';
call_user_func('print_r', $foo);
```

Same goes for call_user_func_array, array_map and other function handling functions. Here is a fairly complicated example of this in action, based on https://stackoverflow.com/a/31411350

The scenario is, the user can filter a list of events in two ways, by postcode/range and by feed (the external feed that was the source of the event). Other filters could be added. Each filter is done separately and the returned event data is serialised before being added to an array. Then we apply array_intersect to that array of serialised objects, which finds any that appear in both filter results. Finally we re-serialise. I daresay there may be better ways of doing this. It's definitely a good idea to serialise arrays before comparing them, if it's just a straight comparison we are after - that is true in this case since we know that any matching result arrays will be identical. 

```php
public function getFiltered(PostcodeRange $range, int $feed_id)
{
    $filterResults = [];
    if(isset($feed_id))
    {
        $filteredByFeed = $this->getWhereWithJoin(array(
            array('name'=>'feed_id', 'comparison'=>'=', 'value'=>$feed_id)
        ));
        $filterResults['filteredByFeed'] = array_map('serialize', $filteredByFeed);

    }
    if(isset($range)) {
        $postcode = $range->getPostcode();
        $rangeKm = $range->getRangeKm();
        $filteredByRange = $this->getEventsInRange($postcode, $rangeKm);
        $filterResults['filteredByRange'] = array_map('serialize', $filteredByRange);
    }

    return array_map('unserialize', call_user_func_array('array_intersect', $filterResults));

}
```



## Variadic functions in PHP

In an earlier draft I said that call_user_func_array allows you to create variadic functions, but that's not actually true as we can see above. It allows us to call functions *dynamically*, but whatever function we call must be supplied with the correct number of arguments. So that's perhaps a limitation in some MVC scenarios e.g. in a CRUD application where we want to pass some but not all of id, order, search terms, page number, postcode, range, and any number of filters.

PHP supports variadic functions via **func_get_args** and its kin (func_get_arg, func_num_args). An example:

```php
function variadicFunction()
{
    $args = func_get_args();
    var_dump($args);
    echo func_get_arg(0);
}

call_user_func_array('variadicFunction', [1,2,3]);
// array(3) { [0]=> int(1) [1]=> int(2) [2]=> int(3) }
// 1
```

func_get_args can of course only be called from within a user-defined function.

## The ... operator

As of PHP 5.6 you can use the ... ~~operator~~ token (the 'splat') to implement variadic functions, instead of func_get_args.

Example from the manual:

```php
function sum(...$numbers) {
    $acc = 0;
    foreach ($numbers as $n) {
        $acc += $n;
    }
    return $acc;
}

echo sum(1, 2, 3, 4);
```

This operator/token can also be used to do this Pythonesque packing/unpacking of parameters:

```
function add($a, $b) {
    return $a + $b;
}

echo add(...[1, 2])."\n";
// 3

$a = [1, 2];
echo add(...$a);
// 3
```

