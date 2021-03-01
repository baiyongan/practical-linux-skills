# Bash Pitfalls Case 16
## foo = bar

No, you can't put spaces around the = when assigning to a variable. This isn't C. When you write foo = bar the shell splits it into three words. The first word, foo, is taken as the command name. The second and third become the arguments to that command.

Likewise, the following are also wrong:

```shell
 foo= bar    # WRONG!
 foo =bar    # WRONG!
 $foo = bar; # COMPLETELY WRONG!

 foo=bar     # Right.
 foo="bar"   # More Right.
```
