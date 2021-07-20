# Bash Pitfalls Case 57
## unset a[0]

When passing an indexed array element to unset, it needs to be quoted. Otherwise, it may be treated as a glob, and expanded against the files in the current directory. If there happens to be a file named a0 then the glob is expanded to a0 and you end up executing unset a0.

```shell
unset 'a[0]'     # Always quote indexed array elements when unsetting.
```
