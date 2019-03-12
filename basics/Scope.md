# Scoping

## Static (Lexical) v Dynamic scoping

Static scoping is most common. Dynamic scoping is uncommon in modern languages.

Static scoping:

> a variable always refers to its top level environment. This is a property of the program text and unrelated to the run time call stack

Dynamic scoping:

> a global identifier refers to the identifier associated with the most recent environment ... 
>
> does not care how the code is written, but instead how it executes. Each time a new function is executed, a new scope is pushed onto the stack.

However, it seems unclear which type of scoping PHP uses. According to https://stackoverflow.com/questions/46902078/does-php-have-static-or-dynamic-scoping it doesn't have either, but has **function scope**.

I think this is borne out by the following example, based on the example in https://msujaws.wordpress.com/2011/05/03/static-vs-dynamic-scoping/

```php
$b = 5;
function foo()
{
    $a = $b + 5;
    return $a;
}
function bar()
{
    $b = 2;
    return foo();
}
echo foo(), " ", bar(); // 5 5
```

According to the example in the link above, with static scoping we should get 10 and 10, and with dynamic scoping 10 and 7. With PHP we get neither; we get 5 and 5.

How about if we do it in Python:

```python
b = 5

def foo():
  a = b + 5
  return a

def bar():
  b = 2
  return foo()

print(foo())
print(bar())
```

That gives us 10 and 10, which means static scoping.

What about Javascript? Then it depends on whether and how we use the var keyword. If we use it throughout like this:

```javascript
var b = 5
function foo(){
 var a = b + 5
 return a
}
function bar(){
 var b = 2
 return foo()
}
document.write(foo())
document.write(bar())
```

...then we get 10 and 10, again static scoping. However, if we omit the *var* in *bar()* then we get 10 and 7 i.e. dynamic scope. So in javascript the var keyword is enforcing static scoping, whereas in Python scoping is always (by default at least) static so we don't need a var declaration.

What *var* is doing in js is binding a variable to a scope. The *b* that is initialised as 2 in bar() exists in bar() only. Because foo() is outside of the scope of bar() it uses the value of b that is in its own scope, i.e. the value that is initialised in the next scope up, the global scope in this case, *even though foo() is called from within bar()*.

So what this boils down to, when considering nested functions, is that with static scoping the value of a variable comes from the scope based on how the code is *written* (hence lexical is the alternate name), but in dynamic scoping it comes from how the code is *executed*.

So what is going on in PHP? Quite simply, $b is always undefined within foo() so $a = $b + 5 always evaluates to 5, unless you give $b a value within foo(), pass a value in, or pull a value in (using *global*).

This is why I have found scope a confusing subject - most of my experience is in PHP, and the way PHP does it is ... *wrong*?

Right or wrong, the point is this: PHP has neither static or dynamic scope, but **function scope**.

Arguably this is a good thing (as long as you get suitable 'undefined variable' warnings) because it automatically limits variable scope hence prevents nasty accidents. In order to make variables available across different scopes you have to explicitly pass them in, which really you would think is a good thing. There is potential to do the wrong thing in PHP by pulling variables into scope with the *global* keyword, but that should simply be avoided.

### The *static* keyword

...can be used with variable declarations to make a variable that is local to a particular function scope, but also persists across visits into that scope. 

