# Bash Pitfalls Case 06
## [ "\$foo" = bar && "\$bar" = foo ]

你不能在旧的 test（或[）命令中使用 &&。 Bash解析器在 \[\[\]\] 或（（））之外看到 &&，并将你的命令分成两个命令，分别在&&之前和之后。 使用以下方法之一：

```shell
[ bar = "$foo" ] && [ foo = "$bar" ] # Right! (POSIX)
[[ $foo = bar && $bar = foo ]]       # Also right! (Bash / Ksh)
```

（请注意，由于陷阱4中讨论的遗留原因，我们反转了 [ 内的常量和变量。我们也可能反转了 [[ 情况，但是扩展需要引用，以防止解释为模式。）这同样适用 到||。 请使用 [[ 代替，或使用两个 [ 命令。
避免如下操作：

```shell
[ bar = "$foo" -a foo = "$bar" ] # Not portable.
```

二进制-a和-o以及（/）（分组）运算符是POSIX标准的XSI扩展。在POSIX-2008中，所有标记都已过时。 它们不应在新代码中使用。[A = B -a C = D]（或-o）的实际问题之一，是POSIX没有指定 test 或 [ 命令的参数超过4个的结果。它可能适用于大多数 shell，但您不能指望它。 如果必须为POSIX shell编写程序，则应使用以 && 运算符分隔的两个test 或 [ 命令。

## 拓展阅读
[old test (or \[) command](http://mywiki.wooledge.org/BashFAQ/031)