# Bash Pitfalls Case 20
## [ bar == "\$foo" ]

`==` 运算符对 POSIX `[` 命令无效。 请改用 `=` 或 `[[` 关键字。

```shell
 [ bar = "$foo" ] && echo yes
 [[ bar == $foo ]] && echo yes
```

在 Bash 中， `[ "$x" == y ]` 被视为是扩展名，这通常会导致 Bash 程序员认为这是正确的语法。
如果你打算 bashism 到底 ，不妨使用 `[[` 来代替。
