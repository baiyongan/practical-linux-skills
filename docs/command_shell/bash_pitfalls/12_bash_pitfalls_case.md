# Bash Pitfalls Case 12
## read $foo

 在 read 命令中，不要在变量名前使用 $，如果要将数据放入名为 foo 的变量中，可以这样操作：

```shell
read foo
```

或者用如下更安全的写法：

```shell
IFS= read -r foo
```

read $foo 将读取一行输入，并将其放入名称在 $foo 中的变量中。如果你实际上，本打算将 foo 引用为其他变量，则这可能很有用； 但是在大多数情况下，这仅仅是一个错误。

## 拓展阅读
[BashFAQ#006](http://mywiki.wooledge.org/BashFAQ/006)
