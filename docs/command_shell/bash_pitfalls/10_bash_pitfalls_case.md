# Bash Pitfalls Case 10
## if [bar="$foo"]; then ...

```shell
[bar="$foo"]     # Wrong!
[ bar="$foo" ]   # Still wrong!
[bar = "$foo"]   # Also wrong!
[[bar="$foo"]]   # Wrong again!
[[ bar="$foo" ]] # Guess what?  Wrong!
[[bar = "$foo"]] # Do I really need to say it....
```

As explained in the previous example, [ is a command (which can be proven with type -t [ or whence -v [). Just like with any other simple command, Bash expects the command to be followed by a space, then the first argument, then another space, etc. You can't just run things all together without putting the spaces in! Here are the correct ways:

```shell
if [ bar = "$foo" ]; then ...

if [[ bar = "$foo" ]]; then ...
```

In the first form, [ is the command name, and bar, =, the expansion of "$foo", and ] are separate arguments to it. There must be whitespace between each pair of arguments, so the shell knows where each argument begins and ends. The second form is similar, except that [[ is a special keyword, which is terminated by the ]]. For more details on the difference between the two, see Bash FAQ 31.