# Bash Pitfalls Case 59
## i=\$(( 10#\$i ))

Forced base 10 interpretation only works with signless numbers. As long as $i contains a string of digits with no leading - or +, everything is fine. But if $i might be negative, this conversion could fail, either noisily (with an error message), or even worse, silently (simply yielding the wrong result).

If there's any chance $i could be negative, use this instead:

```shell
i=$(( ${i%%[!+-]*}10#${i#[-+]} ))
```

For explanations, please see ArithmeticExpression.