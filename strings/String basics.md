# String basics

Simple 'foo' and complex "foo"

Complex string allow embedding of vars, array members, object methods, control characters. 

Use curly brackets to delimit exactly what part of the string is var name.

Use heredoc and nowdoc for multi-line strings, simple/complex.

Use square or curly bracket notation to reference characters in string, e.g. \$foo{1} = 'o'. If you assign \$foo[10] = 'x' the intervening spaces are filled, so \$foo = 'foo      x'.

## Comparison

When PHP compares strings e.g. in \$str1 > \$str2 and using strcmp(), it is the ASCII values that are compared (so all lower case letters come before all upper case letters and 'A' < 'z'). The strcmp() function returns a difference calculation based on ASCII values. The function ord() returns the ASCII value of a character (chr() returns a character corresponding to an ASCII value).

## Searching, manipulating and formatting

### Haystack and needle

The order of haystack and needle in function arguments is not arbitrary, as it sometimes appears.

- For string search functions, the order is always \$haystack then $needle
- For array search functions, the order is always $needle then \$haystack

this has something to do with the underlying C libraries, but that doesn't help me remember which way round is which...