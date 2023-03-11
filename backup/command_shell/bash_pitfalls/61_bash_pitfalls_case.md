# Bash Pitfalls Case 61
## [[ -v hash[\$key] ]]

Here, hash is an associative array. This construct fails because $key is expanded before the array subscript evaluation, and then the whole array plus expanded index is evaluated in a second pass. It will appear to work for simple keys, but it will fail for keys containing quotes, closing square brackets, etc. Even worse, it introduces a CodeInjection if the $key contains CommandSubstitution syntax.

The same problem applies to the test and [ commands, as well as any use of an associative array element in an arithmetic context.

Newer versions of bash (5.0 and higher) have a assoc_expand_once option which will suppress the multiple evaluations. Another choice is to single-quote the entire argument:

```shell
[[ -v 'hash[$key]' ]]
```

This has the advantage of working in older versions of bash as well as newer versions.


