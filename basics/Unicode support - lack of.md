# Unicode support in PHP

PHP does not have native Unicode support. There was an attempt to introduce it with PHP 6, and that is what ultimately killed PHP 6. It didn't help that they were attempting to use UTF-16. That was a reasonable decision when they started out but became less tenable as time went by and UTF-8 became the preferred standard. 

Lack of native Unicode support is a stick that people often use to beat PHP with. The lack of support is a consequence of PHP's development history, and of its success really. Some languages started with native UTF support. PHP didn't - that might be portrayed as amateurish or naïve by some, and there may be something in that, but as much as anything it is because PHP started out as and remains a language of the Web. As such, it is largely concerned with handling text (can I really say that?) and as such having a policy of 1 byte = 1 character makes everything a whole lot easier. That made sense in the early days of the Web when PHP was conceived. 

Some languages less popular than PHP have been re-engineered to have native support for Unicode, but for PHP that just isn't practicable because of its wide use. The amount of things that would be broken by making such a change make it untenable. 

Anyway, what do we actually mean by native Unicode support, or lack thereof?

1. Unicode is a multi-byte encoding (see other notes on character sets and encodings). 
2. PHP strings work on the basis of 1 byte = 1 character (as in ASCII, Latin-1 and other early encoding schemes)
3. ...hence PHP doesn't have native Unicode support

Again, you can see why in the days of PHP's inception, when pretty much everything on the Web was in English and Unicode wasn't a widely accepted standard, it made sense to design things this way. Multi-byte support would have seemed like a massive and unnecessary overhead, if they even considered it.

Note that PHP does have support for Unicode. There are multi-byte versions of some of the basic string handling functions, and there are many functions concerned with character encoding and decoding. But you do have to be careful and you do need to know what you are doing.

Note also, this topic relates to the concept of functions being 'binary safe' or not in PHP (see other notes). Older PHP functions might not be 'binary safe' because they assume that the (string) data they handle is 1 byte = 1 character. So they are fine handling ASCII, or Latin-1, or the lower reaches of Unicode, but strange things will happen if you hand them binary data that includes bytes that do anything other than represent a single character.