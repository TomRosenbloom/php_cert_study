

# Binary safe

Functions in PHP are described as being 'binary safe' or not. What does that actually mean? (And why is it seemingly impossible for anyone in the entire Internet to explain it in a way that I can fully understand?)

In an ideal world, all PHP functions would be binary safe and we'd never need to worry about it. However, older functions may not be binary safe, and that includes (by definition) much of the core of PHP and many well established modules. To re-write everything to make it binary safe would be a massive and massively risky task, so PHP is stuck with this inconvenience.

The concept of being 'binary safe' (in the specifically PHP sense) relates to string handling. 

PHP strings are strings of bytes, one byte per character, and hence supporting of the Latin-1 character set only (or just ASCII?), and not Unicode. For e.g. in an **old** SO comment someone gives this example [1][]:

```php
$str = "R&D - Solution";
$arr = array( "R&D - Solution" => "Research" );
echo $arr[$str];  // did not work
```

This is a familiar scenario for me - perhaps in relation to work with Cyrillic text, or even just student names with unusual characters? This poster's solution btw was to create MD5 hashes of of keys. NB another more recent commenter says the original example would now work.

The reason older core functions in PHP may not be binary safe is because they are written in C and use that languages default means of string representation which is to terminate a string with the null character. Or rather \0 the NULL byte, meaning the character at code point 0 which is null i.e. 'no character'. This is logical enough since a string is supposed to consist of characters. For some reason it is now seen as desirable to allow the use of the null byte within a string - I don't quite get why - and delimit the string by specifying its length rather than using a special character. There must be a damn good reason for this, because making functions binary safe is significantly more work, because for e.g. you have to know and work with two values, the string itself and the length of the string.

I think it must be more than just null bytes that are allowed in a binary safe function. Perhaps I can test this by playing with this example [2][1]:

```php
$str1="web";
$str2="webx00Development";

echo strcoll($str1,$str2); // 0, both strings are equal (Non-binary safe function)

echo strcmp($str1,str2); // less than 0, $str1 less than $str2 (Binary safe function)
```

In fact, running on my Windows machine, the results of this are -1, -14, so whilst they are different, it isn't the case that the non-binary safe one thinks the two strings are the same.

This is really doing my head in.

I found another definition out there [4][], and I'm going to leave it at this:

```
"It means that every single byte is treated with respect: it won't try and convert a linefeed character 0x13 into a carriage return-linefeed pair 0x10x13 (Windows uses the latter to indicate the end of a line in text files, while Unix uses the former); the string won't be cut short if there's a nul character 0x00 in it (which C uses to indicate the end of a string); it won't zero out the high bit in every byte (since pure ASCII characters are all between 0x00 and 0x7f). Stuff like that.

It means you can use it on binary data without weird manglings happening."
```





[1]: https://stackoverflow.com/a/17590910



[2]: https://stackoverflow.com/questions/3264514/in-php-what-does-it-mean-by-a-function-being-binary-safe
[3]: https://stackoverflow.com/questions/36933837/why-are-there-binary-safe-and-binary-unsafe-functions-in-php
[4]: https://board.phpbuilder.com/d/10286686-binary-safe-what-does-it-mean

