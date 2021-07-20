# Bash Pitfalls Case 52
## find . -exec sh -c 'echo {}' \;

This command contains a CodeInjection vulnerability. The filename that is found by find is injected into a shell command and parsed by sh. If the filename contains shell metacharacters like ; or $( ... ) then the filename may be executed as code by `sh'.

The "slowprint" example in the previous Pitfall would have been a CodeInjection bug if the input weren't guaranteed to be integers.

To be more precise, POSIX find does not specify whether an argument which contains more than just {} is expanded. GNU find allows this CodeInjection to occur. Other implementations choose a safer path:

```shell
# uname -a
HP-UX imadev B.10.20 A 9000/785 2008897791 two-user license
# find /dev/null -exec sh -c 'echo {}' \;
{}
```

The correct approach is to separate the filename argument from the script argument:

```shell
find . -exec sh -c 'echo "$1"' x {} \;
```