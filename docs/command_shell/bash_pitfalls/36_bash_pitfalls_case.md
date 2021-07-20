# Bash Pitfalls Case 36
## [ -n \$foo ] or [ -z \$foo ]

When using the [ command, you must quote each substitution that you give it. Otherwise, $foo could expand to 0 words, or 42 words, or any number of words that isn't 1, which breaks the syntax.

```shell
[ -n "$foo" ]
[ -z "$foo" ]
[ -n "$(some command with a "$file" in it)" ]

# [[ doesn't perform word-splitting or glob expansion, so you could also use:
[[ -n $foo ]]
[[ -z $foo ]]
```
