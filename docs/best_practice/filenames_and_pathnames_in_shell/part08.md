# 8 快速了解换行

Newline can be a little tricky to get into a shell variable. You can’t do:

```shell
  newline="$(printf '\n')"
```

Because after the $(...) command is executed, any trailing newline is removed.
One alternative is:

```shell
newline='
'
```

But this can get corrupted by programs that change the encoding of file end-of-lines.
The following is a standards-compliant trick to get newline into a variable:

```shell
newline="$(printf '\nX')"
newline="${newline%X}"
```

That is a pain, obviously. More recently, POSIX added support for $'...'. Most shells, though not all, already support it. On a shell that does, you can do this:

```shell
newline=$'\n'
```
