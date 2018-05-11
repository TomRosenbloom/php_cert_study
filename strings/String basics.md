# String basics

Simple 'foo' and complex "foo"

Complex string allow embedding of vars, array members, object methods, control characters. 

Use curly brackets to delimit exactly what part of the string is var name.

Use heredoc and nowdoc for multi-line strings, simple/complex.

Use square or curly bracket notation to reference characters in string, e.g. \$foo{1} = 'o'. If you assign \$foo[10] = 'x' the intervening spaces are filled, so \$foo = 'foo      x'.