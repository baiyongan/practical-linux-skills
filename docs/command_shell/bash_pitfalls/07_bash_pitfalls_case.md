# Bash Pitfalls Case 07
## [[ $foo > 7 ]]

There are multiple issues here. First, the [[ command should not be used solely for evaluating arithmetic expressions. It should be used for test expressions involving one of the supported test operators. Though technically you can do math using some of [['s operators, it only makes sense to do so in conjunction with one of the non-math test operators somewhere in the expression. If you just want to do a numeric comparison (or any other shell arithmetic), it is much better to just use (( )) instead:

```shell
# Bash / Ksh
((foo > 7))     # Right!
[[ foo -gt 7 ]] # Works, but is uncommon. Use ((...)) instead.
```

If you use the > operator inside [[ ]], it's treated as a string comparison (test for collation order by locale), not an integer comparison. This may work sometimes, but it will fail when you least expect it. If you use > inside [ ], it's even worse: it's an output redirection. You'll get a file named 7 in your directory, and the test will succeed as long as $foo is not empty.

If strict POSIX-conformance is a requirement, and (( is not available, then the correct alternative using [ is

```shell
# POSIX
[ "$foo" -gt 7 ]       # Also right!
[ "$((foo > 7))" -ne 0 ] # POSIX-compatible equivalent to ((, for more general math operations.
```

If the contents of $foo is not sanitised and its contents is not under your control (if for instance it's coming from an external source), then all but [ "$foo" -gt 7 ] constitute an arbitrary command injection vulnerability as the contents of $foo is interpreted as an arithmetic expression (and for instance, the a[$(reboot)] arithmetic expression would run the reboot command when evaluated). The [ builtin requires the operands be decimal integers, so it not affected. But it's critical that $foo be quoted, or you'd still get a command injection vulnerability (for instance with values such as -v a[$(reboot)] -o 8).

If the input to any arithmetic context (including ((, let, array indices), or [[ ... ]] test expressions involving numeric comparisons can't be guaranteed then you must always validate your input before evaluating the expression.

```shell
# POSIX
case $foo in
    ("" | *[!0123456789]*)
        printf '$foo is not a sequence of decimal digits: "%s"\n' "$foo" >&2
        exit 1
        ;;
    *)
        [ "$foo" -gt 7 ]
esac
```

Note that as [/test's arithmetic operator expect decimal integers, 010 for instance would be interpreted as the number 10, not 8 expressed in octal. In bash, [ 010 -gt 8 ] would return true, while [[ 010 -gt 8 ]] and (( 010 > 8 )) would return false.