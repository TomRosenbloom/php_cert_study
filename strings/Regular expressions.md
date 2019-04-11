# Regular expressions

## Basic principles

A regular expression is a formalised way of defining string patterns, which allows for testing matches in text. 

The most common use cases I can think of in PHP are for validation of user input e.g. checking an email address is valid, and for creating custom routes in MVC. In theory they can be used for text searches, but in practice you never do this because there are always higher level methods for doing text searches. [I used regular expressions in XSLT to for e.g. match alternate spellings of words e.g. 'speciali[sz]e' which would match UK and American spellings.

Regular expressions contain a mixture of literal characters and meta-characters, which can define alternate characters, ranges of characters, 'wildcards' etc. etc.

https://www.regular-expressions.info/ is the motherlode for general RE info.

## PCRE syntax

PHP uses PCRE (Perl Compatible Regular Expressions) syntax for regular expressions. It used to support POSIX but that was dropped in PHP 7.

## Delimiters

Delimiters must be used. You can use anything non-alphanumeric, except backslash. You can't use any whitespace character. Forward slash is probably the most commonly used, but you also often see #, %, ~, +. Doesn't really matter but you should obvs avoid characters that are likely to be used in the pattern - you can still use the delimiter character in the pattern but you must escape it with a backslash (backslashes can be added with preg_quote()).

You can use brackets as delimiters if you really want to. Seems like a bad idea because they all also function as meta-characters. If you do use them note you don't need to escape them for their use as meta-characters, only if they represent a literal character.

### Meta-characters

| Meta-character | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| \              | general escape character with several uses                   |
| ^              | assert start of subject (or line, in multiline mode)         |
| $              | assert end of subject or before a terminating newline (or end of line, in multiline mode) |
| .              | match any character except newline (by default)              |
| [              | start character class definition                             |
| ]              | end character class definition                               |
| \|             | start of alternative branch                                  |
| (              | start subpattern                                             |
| )              | end subpattern                                               |
| ?              | extends the meaning of (, also 0 or 1 quantifier, also makes greedy quantifiers lazy (see repetition) |
| \*             | 0 or more quantifier                                         |
| \+             | 1 or more quantifier                                         |
| {              | start min/max quantifier                                     |
| }              | end min/max quantifier                                       |

#### \ escape character

Can be used:

1. to 'neutralise' other meta-characters i.e. if you want to match a literal '\*' you would use '\\*'. NB if you use a backslash before an ordinary character it is simply ignored, so if in doubt, of for clarity, you can use the backslash to ensure or re-enforce that a character stands for itself.
2. to encode non-printing characters e.g. a tab can be represented as \t or \011. See the manual for a load more complicated and rarely used examples.
3. to specify generic character types e.g. \d any decimal digit, \s any whitespace character, \w any 'word' character
4. for 'certain simple assertions' - ?

#### ^ and $ 

These are 'anchors'. In practice you see these appearing at the beginning and end of an expression, but they don't just mark out the beginning and end of the expression - we have delimiters for that - but something more subtle. 

For one thing they test whether a character (or whatever) is at the start or end of a string:

```php
echo preg_match('/o/','foobar'); // 1
echo preg_match('/^o/','foobar'); // 0
```

...but there is more to it than that. I'll be coming back to this when I get on to 'assertions'.

#### .

Matches one character

#### Character class

Enclosed by square brackets, a character class matches a single character, according to the rules within the brackets. For e.g. [aeiou] matches any vowel

Character classes have their own set of meta-characters, one of which is the same as a normal meta-character but does something different within a character class, one of which does the same in either context, and one which has meaning only within a character class. Not to be confusing or anything.

| Meta-character | Description                                       |
| -------------- | ------------------------------------------------- |
| \              | general escape character                          |
| ^              | negate the class, but only if the first character |
| -              | indicates character range                         |

So \[^aeiou] matches any character that is *not* a vowel, [1-9] matches any number between 1 and 9, [a-z]  any letter (lower case normally, but this could be changed with a modifier after the final delimiter - more on that elsewhere).

Character types can be used in character classes. There are also a bunch of POSIX named classes that can still be used e.g. *alnum*, *alpha*, etc. but they must be enclosed in square brackets and colon e.g. [:alpha:]

#### |

The vertical bar 'or' character represents alternative patterns, e.g. red|blue|yellow matches any of the prime colours. You can have any number of alternatives.

#### ()

Round brackets delimit subpatterns, which can do two things:

1. the obvious thing i.e. 'localising a set of alternatives', e.g. from the manual cat(aract|erpillar)
2. create a *capturing* subpattern

##### Capturing subpatterns

if you supply preg_match() for e.g. with its third parameter *matches*, then that parameter is returned with the results of the matching operation. So:

```php
preg_match('/(foo)(bar)(baz)/', 'foobarbaz', $matches);
print_r($matches); //Array ( [0] => foobarbaz [1] => foo [2] => bar [3] => baz )
```

Note what happens if you add extra brackets round the whole pattern:

```php
preg_match('/((foo)(bar)(baz))/', 'foobarbaz', $matches);
print_r($matches); //Array ( [0] => foobarbaz [1] => foobarbaz [2] => foo [3] => bar [4] => baz )
```

The example from the manual: 

```php
preg_match('/the ((red|white) (king|queen))/', 'the red king', $matches);
print_r($matches); // Array ( [0] => the red king [1] => red king [2] => red [3] => king )
```

...and this clarifies things I think:

```php
preg_match('/(((foo)(bar)(baz)))/', 'foobarbaz', $matches);
print_r($matches); // Array ( [0] => foobarbaz [1] => foobarbaz [2] => foobarbaz [3] => foo [4] => bar [5] => baz )
```

...adding yet another set of brackets means another match for foobarbaz is returned, but that makes sense.

You can **supress the capturing of sub-patterns** in cases where this dual function of round brackets is unwanted:

```php
preg_match('/(?i:saturday|sunday)/', 'sunday', $matches);
print_r($matches); // Array ( [0] => sunday )
```

You can also specify names for the captured subpatterns:

```php
preg_match('/the ((?P<colour>red|white) (king|queen))/', 'the red king', $matches);
print_r($matches); // Array ( [0] => the red king [1] => red king [colour] => red [2] => red [3] => king )
```

A final example from the manual:

```php
preg_match('/(?:(Sat)ur|(Sun))day/', 'Sunday', $matches);
print_r($matches); // Array ( [0] => Sunday [1] => [2] => Sun )

preg_match('/(?|(Sat)ur|(Sun))day/', 'Sunday', $matches);
print_r($matches); // Array ( [0] => Sunday [1] => Sun )
```

What's happening here? The example shows how you can use *(?|* syntax to 'have multiple matching but alternating subgroups'. So the aim here is to get back the subpattern that gives the three letter abbreviation of the day, 'Sat' or 'Sun'.

Best way to understand this is to try a few more variations. If we go with the naïve pattern *((Sat)ur|(Sun))day*, it doesn't work for Saturday or Sunday:

```php
preg_match('/((Sat)ur|(Sun))day/', 'Saturday', $matches);
print_r($matches); // Array ( [0] => Saturday [1] => Satur [2] => Sat )

preg_match('/((Sat)ur|(Sun))day/', 'Sunday', $matches);
print_r($matches); // Array ( [0] => Sunday [1] => Sun [2] => [3] => Sun )
```

so we add *?:* to supress the extraneous match. That works for Saturday but not for Sunday:

```php
preg_match('/(?:(Sat)ur|(Sun))day/', 'Saturday', $matches);
print_r($matches); // Array ( [0] => Saturday [1] => Sat )

preg_match('/(?:(Sat)ur|(Sun))day/', 'Sunday', $matches);
print_r($matches); // Array ( [0] => Sunday [1] => [2] => Sun )
```

This is really badly explained in the manual... fuck it, come back to it later.

#### ? + * { }

The examples above show how *?* extends the meaning of the opening bracket character. It is also a **quantifier for repetition** along with * and + and curly brackets.

The basic way of quantifying repetition is to use curly brackets containing numbers for min and max, separated by a comma (the second number can be omitted). The other quantifier characters are abbreviations of this format. Quantifiers can follow any of:

- a single character
- the . metacharacter
- a character class
- a back reference
- a subpattern in parentheses

You can omit: the second number but not the comma - meaning there is no upper limit, or the second number *and* the comma, meaning the number specifies an exact number of matches. 

Examples:

- z{2,4} matches 'zz', 'zzz', or 'zzzz'
- z{2,} matches 'zz', 'zzz', 'zzzz' ...
- z{2} matches 'zz' only

The ? + and * symbols are abbreviations of the above:

| Symbol | Equivalent | Meaning                |
| ------ | ---------- | ---------------------- |
| *      | *{0,}*     | any number of, or none |
| *+*    | *{1,}*     | one or more of         |
| *?*    | *{0,1}*    | one or none of         |

##### Greediness/laziness

Quantifiers are by default 'greedy', meaning they match as much as possible. This can be a problem e.g. when matching code comments between `/*` and `*/`, if you use pattern `/|*.*|*/` on the subject `/* a comment */ some code /* another comment */` this will fail because the whole subject will be matched. This can be fixed using ? to enforce laziness:  `/|*.*?|*/` . Note the ? doesn't so much enforce laziness as reverse the current behaviour. It is possible to set option PCRE_UNGREEDY in which case the ? will enforce greediness.

### Back references

> Outside a character class, a backslash followed by a digit greater than 0 (and possibly further digits) is a back reference to a capturing subpattern earlier (i.e. to its left) in the pattern, provided there have been that many previous capturing left parentheses.

Example:

*`(sens|respons)e and \1ibility`* matches "sense and sensibility" and "response and responsibility" but not "sense and responsibility".

### Assertions

### Once-only subpatterns

### Conditional subpatterns

### Recursive patterns





## Appendix: why 'regular expressions'?

### Formal languages

> **Natural languages** are the languages that people speak, such as English, Spanish, and French. They were not designed by people (although people try to impose some order on them); they evolved naturally.
>
> **Formal languages** are languages that are designed by people for specific applications. For example, the notation that mathematicians use is a formal language that is particularly good at denoting relationships among numbers and symbols. Chemists use a formal language to represent the chemical structure of molecules
>
> http://interactivepython.org/courselib/static/thinkcspy/GeneralIntro/FormalandNaturalLanguages.html

Programming languages are of course formal languages.

The **syntax** of a formal language is defined via **tokens** and **structure**. Simply put, the tokens are the elements e.g. pre-defined characters and symbols, and the structure is the rules for how they may be put together. **Parsing** is the process of applying the structure rules to extract the tokens and discover the meaning i.e. the **semantics**. In fact this is also true of natural languages. The differences between natural and formal languages are found elsewhere. A formal language is strict, unambiguous, literal etc.

Tokens and structure = alphabet and grammar. Syntax = tokens/alphabet + structure/grammar. Formal grammars (the grammars of formal languages) can be ordered according to the **Chomsky Hierarchy**.

> - **Regular grammars**, which retain no past state knowledge from input string to output string
>
> - **Context-free grammars**, which retain only recent state knowledge from input string to output string
>
> - **Context-sensitive grammars**, which keep all past state knowledge from input string to output string
>
> - **Unrestricted** (or **recursively enumerable**) **grammars**, which have all state knowledge and thus can create every output string imaginable from a given input string
>
>   https://medium.freecodecamp.org/exploring-the-linguistics-behind-regular-expressions-596fab41146

[this ought to go the other way up really, since the bottom one is the ultimate super-set]

What is this 'recursively enumerable' business? This is some deep shit connected with computation theory and Turing machines. The first step to understanding it is that 'recursive' in this context is not the same as in 'recursive function' (though there might be some connection somewhere). Other than that, I'll put it aside for now...

### Metacharacters

There's some kind of idea that a regular grammar just needs operations/rules for alternation, concatenation and repetition, and nothing else. That's if we understand a grammar as being a way of creating outputs (terminals) from inputs (non-terminals). So that is seeing a grammar as being a generator of language. That's non-intuitive to me because I naturally think of a grammar in the opposite sense as a recogniser of language. Both are valid. On that understanding, the inputs or *expression* 0|1 produce the set of possible outputs {0,1} (alternation), 0•1 produces {01} (concatenation), and - for example - 0•1* produces {0, 01, 011, 0111, ...}.

This is half understood garbage. The article I'm following isn't very clear. Hopefully with a few other sources it will fall into place... anyway the point is, this is why we see the term 'metacharacters' describing for e.g. the dot in the regex pattern .abc (any single character followed by abc), because that could also be written in terms of 'alternation' as [a|b|c| ... |z]abc.





