Using increment/decrement postfix/prefix

This is another one of those weird things that doesn't behave as you would expect.

```
$x = 5;
echo $x;
echo $x+++$x++;
echo $x;
echo $x---$x--;
echo $x;
```

What's the output of this?

Surprisingly (with the addition of commas and spaces between the echo statements), it's 5, 11, 7, 1, 5.

First of all for clarity \$x+++\$x++ is equivalent to \$x++ + \$x++. But still I thought that would come to 13, or possibly 12, but 11 was a surprise. The reason is that with the increment done 'postfix' i.e. \$x++, rather than the alternative 'prefix', ++\$x, the value used is the value *before* the increment. That is, \$x++ means, 'use \$x, then increment it'.

So I still don't quite get this but I think the way \$x+++\$x++ goes is:

1.  take the value of \$x on the left hand side of the addition operation (5) and use that as the first operand

2.  increment \$x (6)

3.  use the new 'current' value of \$x (6) on the right hand side as the second operand

4.  perform the addition of these operands (5 + 6)

5.  increment the current value of \$x (7)

On the other hand, using prefix ++\$x + ++\$x, does exactly what you would expect, 6 + 7 = 13 (NB you can't push this together as ++\$x+++\$x. That produces a parse error).

NB this is not a PHP specific issue but something common to C and other related languages.
