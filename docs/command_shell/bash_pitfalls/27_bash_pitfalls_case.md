# Bash Pitfalls Case 27
## local var=\$(cmd)

When declaring a local variable in a function, the local acts as a command in its own right. This can sometimes interact oddly with the rest of the line -- for example, if you wanted to capture the exit status ($?) of the CommandSubstitution, you can't do it. local's exit status masks it.

Another problem with this syntax is that in some shells (like bash), local var=$(cmd) is treated as an assignment, meaning the right hand side is given special treatment, just like var=$(cmd); while in other shells (like dash), local var=$(cmd) is not treated as an assignment, and the right hand side will undergo word splitting (because it isn't quoted).

Quoting the right hand side will work around the word splitting issue, but not the exit status masking issue. For both reasons, it's best to use separate commands for this:

```shell
 local var
 var=$(cmd)
 rc=$?
```

Both issues are also true of export.

The next pitfall describes another issue with this syntax:


