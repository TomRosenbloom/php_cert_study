# Character sets and encodings

When you see strange characters in web pages like random question marks, Ã, the euro symbol, and so on, that is an indication that the character encoding of the web page doesn't match the character set of the content being displayed.

A character set is just an abstract notion like 'all the characters used in English plus some useful symbols and control characters - as many as we can represent using 7 bits i.e. 128 of them' (which was ASCII), 'all the characters used in English plus some extra accented characters used in European languages, some useful symbols and control characters - as many as we can represent using a single byte i.e. 255 of them' (Latin-1) or 'all the characters used by all the languages in the world and any number of extra symbols' (Unicode).

A character encoding is a scheme for representing a particular character set so it can be stored and displayed consistently. With a single byte system like ASCII this is v simple - each character is assigned an integer number (its 'code point'), so a string var is just a string of bytes (with a terminator). You couldn't do anything so simple with Unicode because of the sheer number of characters it represents. The obvious solution would be to use more bytes to represent characters (multi-byte encoding) hence increasing the number of code points, but that is wasteful because for most characters the bytes on the left won't be being used. The answer is to use a variable-byte encoding, which is what UTF-8 is.

In UTF-8 the code-points 0-127 are the same as they were in ASCII, so for these characters you only need a single byte. The code-points above 127 are divided into two ranges that between them can be used to represent the entire Unicode character set (and anything that might be added to it) in way that is analogous to the use of a Shift key. The first range 128-192 are the keys, and the range 192-247 are the shift keys. Using a combination of one or more shift keys plus a key gives the code point of a character. The number of bytes used to encode a character varies according to the number of 'shift' keys used.

Examples:

- Cyrillic **Я** is code point 1071 which is encoded in two bytes, 208 followed by 175, using the calculation (208%32)*64 + (175%64) = 1071
- the Chinese ⾀ character is 226 followed by 190 then 128 (characters 224-239 act like double shifts)

So this explains why you might see an Ã symbol (followed by some other random char). This is when a character was encoded using UTF-8 but is rendered with say ISO-8859-1 (the encoding for Latin-1). What you are seeing is the two UTF-8 bytes that make a single Unicode character being interpreted as two separate Latin-1 characters, where the Ã is at the code point used by a UTF-8 shift character.