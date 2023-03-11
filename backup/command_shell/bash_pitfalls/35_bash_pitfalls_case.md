# Bash Pitfalls Case 35
## if [[ \$foo =~ 'some RE' ]]

The quotes around the right-hand side of the =~ operator cause it to become a string, rather than a RegularExpression. If you want to use a long or complicated regular expression and avoid lots of backslash escaping, put it in a variable:

```shell
re='some RE'
if [[ $foo =~ $re ]]
```

This also works around the difference in how =~ works across different versions of bash. Using a variable avoids some nasty and subtle problems.

The same problem occurs with pattern matching inside [[:

```shell
[[ $foo = "*.glob" ]]      # Wrong! *.glob is treated as a literal string.
[[ $foo = *.glob ]]        # Correct. *.glob is treated as a glob-style pattern.
```
