# Bash Pitfalls Case 05
## cd $(dirname "$f")

This is yet another quoting error. As with a variable expansion, the result of a CommandSubstitution undergoes WordSplitting and pathname expansion. So you should quote it:

```shell
cd -P -- "$(dirname -- "$f")"
```

What's not obvious here is how the quotes nest. A C programmer reading this would expect the first and second double-quotes to be grouped together; and then the third and fourth. But that's not the case in Bash. Bash treats the double-quotes inside the command substitution as one pair, and the double-quotes outside the substitution as another pair.

Another way of writing this: the parser treats the command substitution as a "nesting level", and the quotes inside it are separate from the quotes outside it.