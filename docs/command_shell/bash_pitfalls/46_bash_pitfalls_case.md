# Bash Pitfalls Case 46
## read num; echo \$((num+1))

Always validate your input (see BashFAQ/054) before using num in an arithmetic context as it allows code injection.

```shell
$ echo 'a[$(echo injection >&2)]' | bash -c 'read num; echo $((num+1))'
injection
1
```
