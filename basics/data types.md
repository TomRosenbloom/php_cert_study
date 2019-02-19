# Data types

## Data type categories introduction

There's a fair bit of contention about data type category definitions, and the categories overlap (because different categories are used in different contexts). 

Also, you could go round and round all day trying to come up with a definitive list of all possible categories of data type. This is how Wikipedia has it (abridged):

- Primitive
  - Boolean
  - Numeric
    - Integer
    - Float
- Composite or 'derived'
  - Data structures
    - Array
    - Tuple
    - Object
  - Enumerations
  - Strings and text
    - Character
    - String
- Other types
  - Pointers & references
  - Function types
- Abstract
  - Queue
  - Set
  - Stack
  - Tree
  - Graph
  - List
  - Hash, map or associative array
  - Smart pointer
- Utility types

Further down I've started writing definitions of some of these, plus other styles of categorisation e.g. native, atomic, compound etc. but I don't want to get too bogged down in this, and I want to keep it PHP-specific.

## Data type categories in PHP

What led me down this rabbit hole was looking into 'function handling functions', and specifically call_user_func_array(), which is often at the heart of an MVC application:

```php
call_user_func_array(callable $callback, array $param_arr);
```

In this code example we have a mysterious data type hint for 'callable', as well as the less mysterious 'array'. Callable is one of PHP's **compound** types, the complete list being:

- array
- object
- callable
- iterable

PHP's **scalar** types are:

- integer
- boolean
- float
- string (a compound type in most languages)

In addition there are two **special** types:

- [resource](http://php.net/manual/en/language.types.resource.php)
- [NULL](http://php.net/manual/en/language.types.null.php)

And some **pseudo-types**:

- [mixed](http://php.net/manual/en/language.pseudo-types.php#language.types.mixed)
- [number](http://php.net/manual/en/language.pseudo-types.php#language.types.number)
- [callback](http://php.net/manual/en/language.pseudo-types.php#language.types.callback) (aka [callable](http://php.net/manual/en/language.types.callable.php))
- [array|object](http://php.net/manual/en/language.pseudo-types.php#language.types.array-object)
- void

## Data type categories in general

### Scalar

Not quite the same thing as 'primitive', apparently.

Contrasted with compound types, these are types that take a single value:

- integer
- boolean
- float

In some peoples' view, values that can express scale, but I think that might be a misconception:

> I was taught (a very long time ago in school) that the term was derived from 'scalar processor' in contrast to a 'vector processor'. A scalar processor is a CPU that can only handle one piece of data at a time. These processors were/are named after the arithmetic terms. Interestingly enough, when you look up 'scalar' on wikipedia, you get redirected to 'variable' https://stackoverflow.com/questions/6623130/scalar-vs-primitive-data-type-are-they-the-same-thing

In PHP we have another scalar type:

- string

In most languages a string would be a compound type.

### Compound

Higher level types that are made up of using scalar types, e.g. array is the most obvious.

### Variants of Scalar/Compound

#### Native

Types that are native to a particular language

#### Atomic/Composite

Same as Scalar/Compound?

#### Primitive

A basic building block of other data types. In practice the same as Scalar, but a conceptually distinct categorisation.

### Function Types

Not clear what this means - could be 'user-defined' types i.e. where you type hint for a class of your own creation, but it could be something more esoteric to do with functional programming.

### Abstract Types

Stacks, queues, trees, lists etc. That is, a type of data (or a way of ordering data - is that necessarily the same thing?) that is universally recognised and implementable in any language, but where the means of implementing is not universal (and may even be implemented in different ways in the same language?). 