# Bash Pitfalls Case 25
## function foo()

This works in some shells, but not in others. You should never combine the keyword function with the parentheses () when defining a function.

Bash (at least some versions) will allow you to mix the two. Most of the shells won't accept that (zsh 4.x and perhaps above will - for example). Some shells will accept function foo, but for maximum portability, you should always use:

```shell
 foo() {
  ...
 }
 ```
 