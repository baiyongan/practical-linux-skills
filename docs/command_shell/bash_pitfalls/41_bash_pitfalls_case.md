# Bash Pitfalls Case 41
## content=\$(<file)

There isn't anything wrong with this expression, but you should be aware that command substitutions (all forms: `...`, \$(...), \$(<file), `<file`, and \${ ...; } (ksh)) remove any trailing newlines. This is often inconsequential or even desirable, but if you must preserve the literal output including any possible trailing newlines, it gets tricky because you have no way of knowing whether the output had them or how many. One ugly but usable workaround is to add a postfix inside the command substitution and remove it on the outside:

```shell
absolute_dir_path_x=\$(readlink -fn -- "\$dir_path"; printf x)
absolute_dir_path=\${absolute_dir_path_x%x}
```

A less portable but arguably prettier solution is to use read with an empty delimiter.

```shell
# Ksh (or bash 4.2+ with lastpipe enabled)
readlink -fn -- "\$dir_path" | IFS= read -rd '' absolute_dir_path
```

The downside to this method is that the read will always return false unless the command outputs a NUL byte causing only part of the stream to be read. The only way to get the exit status of the command is through PIPESTATUS. You could also intentionally output a NUL byte to force read to return true, and use pipefail.

```shell
set -o pipefail
{ readlink -fn -- "\$dir_path" && printf '\0'; } | IFS= read -rd '' absolute_dir_path
set +o pipefail
```

This is somewhat of a portability mess, as Bash supports both pipefail and PIPESTATUS, ksh93 supports pipefail only, and only recent versions of mksh support pipefail, while earlier versions supported PIPESTATUS only. Additionally, a bleeding-edge ksh93 version is required in order for read to stop at the NUL byte.


