# Error handling

Three ways to handle errors:

1. die() or exit()
2. throw exception
3. trigger_error()

## die or exit

die() is an alias of exit() so from hereon in just 'exit'.

exit is a language construct rather than a function. You can use exit on its own (without parenthesis) or with a single parameter, which can be an error message string or an 'exit status' integer. The exit status integer must be between 0 and 254 (255 is reserved). There isn't any significance in the value - that's up to you - except 0 is 'used to terminate the program successfully'. As it's not a function there is no return value.

This is the crudest way of handling errors, and not something you should use in production code. It's just for debugging during development.

## exceptions

Throwing an exception is more about creating an alternative course of action when something doesn't work as expected (or different courses of action for different types of failure).

Exception should be *thrown* within a try catch.

## trigger_error()

This is historically an older way of handling errors (compared with exceptions). If PHP was designed now from the ground up there wouldn't be two different ways of handling errors (triggering errors v exceptions). It's highly unlikely to see any new code that uses trigger_error instead of exceptions. PHP was behind many other languages wrt implementing exceptions. So error triggering is relatively a dated concept - and is more associated with procedural code - however this has not only been left in the language but also continues to be developed, to the extent that errors can be made to behave (more or less) like exceptions. I think. Or trigger_error is now intended to be used *in conjunction with* exceptions?

## Error class in PHP7