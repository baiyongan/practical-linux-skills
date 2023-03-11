# Bash Pitfalls Case 16
## foo = bar

不，在分配给变量时，您不能在 `=` 周围放置空格。 Bash 不是 C。当你写 `foo = bar` 时，shell 把它分成三个词。 第一个单词 `foo` 被用作命令名称。 第二个和第三个成为该命令的参数。

同样，以下也是错误的：

```shell
 foo= bar    # WRONG!
 foo =bar    # WRONG!
 $foo = bar; # COMPLETELY WRONG!

 foo=bar     # Right.
 foo="bar"   # More Right.
```
