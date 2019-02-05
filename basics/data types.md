# Data types

## Data type categories

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



### Variants of Scalar/Compound

#### Native

Types that are native to a particular language

#### Atomic/Composite

Same as Scalar/Compound?

#### Primitive

A basic building block of other data types. In practice the same as Scalar, but a conceptually distinct categorisation.

### Function Types

### Abstract Types

