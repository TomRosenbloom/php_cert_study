# Bitwise operators

## Bit shifting

There are two shift operators, shift right \>\> and shift left \<\<.

50 is 00110010, so 50 \<\< 1 is 01100100, i.e. 100 (double), and 50 \>\> 1 is 00011001, 25 (half)

50 <\< 2 is 200, and 50 >> 2 is 12 i.e. 00001100 binary (the unit value having dropped off the end)

So in Q9 *echo 10 <=> 10 << 1* the output should be -1