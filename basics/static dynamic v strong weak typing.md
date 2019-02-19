# Static/dynamic vs strong/weak typing

PHP is dynamically typed and also weakly typed, but these two don't necessarily go together since they mean different things.

## Static/dynamic

Dynamic:

> Variables’ type declarations are not mandatory and they will be generated/inferred on the fly, by their first use.

Static:

> Variable declarations are mandatory before usage, else results in a compile-time error.

It's reasonable to say that dynamic typing goes with interpreted languages (or rather, a language being run in interpreted mode) and static with compiled languages. That's evident from the quotes above.

PHP's origins are as a scripting language and (hence) is interpreted. By that token, and perhaps others, it is dynamically typed. 

It is possible for any given language to be run interpreted or compiled - I think this is true to say. Certainly it is possible to compile PHP, but all compilation really means is to translate your original code from its source into something else (lower level, logically) before running it. And yes there are PHP compilers available but it's a v unusual way to run PHP. Some large sites compile PHP for performance gains. There is a potential performance cost of compiling, which is that ay changes to the code are going to be more time consuming. On that basis it might make sense to compile legacy PHP, but less so PHP that is in active development. [See this on Quora](https://www.quora.com/Why-should-we-use-a-compiler-to-compile-PHP-codes-to-C++-instead-of-using-C++-directly-which-is-supposed-to-be-compiled-into-the-machine-code-directly) about some reasons that PHP may be compiled. Note from [this other Quora article](https://www.quora.com/Does-PHP-compile-How) that the PHP interpreter creates 'optimised runtime Opcodes' which can be cached, and that (caching) is maybe a more rational way of achieving performance gains for v heavily used PHP. In the final analysis, as long as you are not hugely indebted to an existing PHP codebase then it would always make more sense to use a different language if want to run compiled.

## Strong/Weak

Strong:

> Once a variable is declared as a specific data type, it will be bound to that particular data type. You can explicitly cast the data type though.

Weak:

> Variables are not of a specific data type. However it doesn’t mean that variables are not “bound” to a specific data type. In weakly typed languages, once a block of memory is associated with an object it can be reinterpreted as a different type of object.

PHP is weakly typed. 

## Examples

Below (and above quoted definitions) from https://blogs.agilefaqs.com/2011/07/11/dynamic-typing-is-not-weak-typing/

![img](http://blogs.agilefaqs.com/wp-content/uploads/2009/09/strongweakstaticdynamic_type-300x202.png)

## Good or bad?

People often criticize PHP for being dynamically and weakly typed, which is often thought to be a disastrous combination.

There is tons of opinionated stuff out there, particularly stuff slating weak typing. But the pragmatic reality is that for web development, which is the domain of PHP (and JavaScript, another 'offender') weak typing is not such a bad thing and does have some benefits (e.g. because user input is always in string form).

One definite downside of weak typing in PHP is the constant need to check for the types of things with functions like is_numeric, is_object etc. But you can (now) get round this with type hinting.