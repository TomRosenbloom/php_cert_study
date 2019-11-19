# Case sensitivity

There doesn't seem to be any discernible logic to the case-sensitivity rules in PHP.

Language constructs and keywords are case insensitive, so 'echo' and 'ECHO' for e.g. are both fine, though it is conventional to use lower case. Pre-defined functions are also case insensitive, but it is conventional to use them as defined in the manual e.g. 'print\_r()' not 'print\_R()'.

User-defined function names are also case insensitive, as are class names, class methods and namespaces.

I suppose the sort of logic (or at least a way of remembering) is that the things that are case sensitive are variables and things *like* variables e.g. constants, array keys, class properties. I'm saying they are like variables in the sense that they are things that have a value and a name (perhaps in the technically they might be things that are stored via symbol table/zval?)

## Case sensitive

-   variables

-   constants

-   array keys

-   class properties

-   class constants

## Case insensitive (both user defined and PHP defined)

-   functions

-   class constructors, class names

-   class methods

-   namespaces

-   keywords and constructs (if, else, null, foreach, echo etc.)
