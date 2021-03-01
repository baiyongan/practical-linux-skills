# Bash Pitfalls Case 18
## su -c 'some command'

This syntax is almost correct. The problem is, on many platforms, su takes a -c argument, but it's not the one you want. For example, on OpenBSD:

```shell
 $ su -c 'echo hello'
 su: only the superuser may specify a login class
```

You want to pass -c 'some command' to a shell, which means you need a username before the -c.

```shell
 su root -c 'some command' # Now it's right.
```

su assumes a username of root when you omit one, but this falls on its face when you want to pass a command to the shell afterward. You must supply the username in this case.