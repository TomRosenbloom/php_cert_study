## Ternary Operator

A way of compacting if-then-else constructions:

condition ? expression1 : expression2;

'expression1' is executed if the condition is true. You can omit this true value, creating an 'Elvis' operator:

condition ?: expression;

And it can be used like this:

```
$a = true;
$b = $a ?: 'foo';
echo $b; // 1
```

So what is happening there is 'if \$a evaluates to true, then make \$b equal \$a', or 'if isset(\$a) then \$b = \$a else \$b = 'foo''. 

But no: 'This shortened version of the ternary operator is not suitable for testing if a variable exists, as the interpreter will throw a warning in this case'. Which is an important warning, because you might otherwise think that you can use the Elvis to assign default values i.e. if this value is not set (evaluates to false) assign a value.

So more precisely what the example does is (1) evaluate the expression and assign the value to $b, and (2) the expression returns the value of $a because, as $a exists, the expression '$a' evaluates to True. If you make $a = "bar", it returns "bar", the value of $a (so the original example is slightly ambiguous). If you make $a = "", you get "foo", because empty string evaluates to False, but if you don't set a value for $a you get an undefined variable notice (followed by "foo"). So you *can* use elvis to set a default value to an undefined var with `$a = $a ?: "foo"`, but you don't because that will also generate the error notice. It's also not really correct because it doesn't distinguish between empty string (and other things that evaluate to False) and an undefined variable, which are two very different things.

## Null Coalescing Operator [PHP 7]

*This* is what you should use (as a ternary operator) to test if a variable exists/set a default value.

\$sortDir = \$\_GET\['sort\_dir'\] ?? 'ASC';

These can be chained:

\$sortDir = \$\_GET\['sort\_dir'\] ?? \$defaultDir ?? 'ASC';

So, you can go through a succession of vars before ending with a concrete default value.

[Seems like the Elvis operator is just something to be avoided - is there any case for using it? https://stackoverflow.com/questions/34571330/php-ternary-operator-vs-null-coalescing-operator]

A tricky example:

```
$a = "apples" <=> "bananas";
$b = $a ?? $c ?? 10;
echo $b; // -1
```

So what is happening here is, if \$a is set, make \$b equal to \$a. \$a *is* set, with a value of -1. The temptation is to think that this -1 will be interpreted as condition=False, so echo \$b would return 10 (\$c not being set).

But if you do this:

```
$c = 'foo';
$a = "apples" <=> "bananas";
$b = true ?? $c ?? 10;
echo $b;
```

...you get 1. Same if the condition is \$a == -1. I do not understand what is going on here.

```
$a = "apples" <=> "bananas";
$b = $c ?? 'bar' ?? 10;
echo $b;
```

This returns 'bar'.

If the first expression is set, then \$b takes the value of that expression.

'if this var is set, take its value', which is not the same as 'if this *condition* is true...'.

## Ternary echo-ing

echo(*condition*) ? *condition true* : *condition false*;

```php
echo($data['post_vars']['feed'] == $feed['id']) ? 'selected = "selected"' : '';
```

