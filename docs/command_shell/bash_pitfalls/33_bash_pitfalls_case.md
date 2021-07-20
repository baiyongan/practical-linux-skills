# Bash Pitfalls Case 33
## for i in {1..\$n}

The BashParser performs BraceExpansion before any other expansions or substitutions. So the brace expansion code sees the literal $n, which is not numeric, and therefore it doesn't expand the curly braces into a list of numbers. This makes it nearly impossible to use brace expansion to create lists whose size is only known at run-time.

Do this instead:

```shell
for ((i=1; i<=n; i++)); do
...
done
```

In the case of simple iteration over integers, an arithmetic for loop should almost always be preferred over brace expansion to begin with, because brace expansion pre-expands every argument which can be slower and unnecessarily consumes memory.


