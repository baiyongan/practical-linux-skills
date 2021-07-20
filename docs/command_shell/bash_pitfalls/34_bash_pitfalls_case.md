# Bash Pitfalls Case 34
## if [[ \$foo = \$bar ]] (depending on intent)

When the right-hand side of an = operator inside [[ is not quoted, bash does pattern matching against it, instead of treating it as a string. So, in the code above, if bar contains *, the result will always be true. If you want to check for equality of strings, the right-hand side should be quoted:

```shell
if [[ $foo = "$bar" ]]
```

If you want to do pattern matching, it might be wise to choose variable names that indicate the right-hand side contains a pattern. Or use comments.

It's also worth pointing out that if you quote the right-hand side of =~ it also forces a simple string comparison, rather than a regular expression matching. This leads us to: