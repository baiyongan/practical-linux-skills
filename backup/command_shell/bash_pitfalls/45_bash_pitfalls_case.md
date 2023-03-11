# Bash Pitfalls Case 45
## y=\$(( array[\$x] ))

The code given to an arithmetic expansion or compound command undergoes an initial pass of expansions and substitutions to generate the text to be parsed and evaluated as an arithmetic expression. This must be handled carefully.

For example, this expression is stitched together by expanding one code fragment into another.

```shell
$ x='$(date >&2)'        # redirection is just so we can see everything happen
$ y=$((array["$x"]))       # Quotes don't help. The array doesn't even have to exist
Mon Jun  2 10:49:08 EDT 2014
```

Next, the expanded string is passed to the arithmetic processor, which will need to get a reference to the array variable in the shell's internal symbol table using a lookup function to resolve variable's "name". This name resolver takes a string - array[$(date >&2)] - consisting of the name, including the index and all literal code within brackets, just like e.g. read or printf -v do with variable names passed as arguments. The variable resolver performs expansions, including command substitution, to resolve the index.

(For numeric indexed arrays, the lookup function next evaluates the expanded text of the index as an arithmetic expression. Consequently, mutually recursive variable lookups and arithmetic expansions can occur to any depth (up to Bash's defined limit), any of which can produce unintended side-effects.)

Most of the time, there is no need to use any kind of expansion within an arithmetic expansion. Use variable names directly in the expression (no $) wherever possible (i.e. except for positional parameters and POSIX "special variables"). Validate variables before using them and assure no expansion generates anything but a numeric literal - most issues are automatically avoided.

Escape any expansions to pass them into the expression without expanding them first:

```shell
# Typical task reading some columns into an associative array.
typeset -A arr
printf -v offset '%(%s)T' -1

while IFS=' ' read -r x y; do
    [[ $x $y == +([0-9]) +([0-9]) ]] # validate input (see next pitfall)
    (( arr[\$(date -d "@$x" +%F)] = y - offset )) # Escaped substitution passes the entire expression literally.
done
```

Another option is to use let with single-quoted arguments. ((expr)) is equivalent to let "expr" (double-quoted args).


