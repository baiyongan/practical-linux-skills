# Bash Pitfalls Case 32
## printf "\$foo"

This isn't wrong because of quotes, but because of a format string exploit. If $foo is not strictly under your control, then any \ or % characters in the variable may cause undesired behavior.

Always supply your own format string:

```shell
printf %s "$foo"
printf '%s\n' "$foo"
```