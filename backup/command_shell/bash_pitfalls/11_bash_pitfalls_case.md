# Bash Pitfalls Case 11
## if [ [ a = b ] && [ c = d ] ]; then ...

Here we go again. [ is a command. It is not a syntactic marker that sits between if and some sort of C-like "condition". Nor is it used for grouping. You cannot take C-like if commands and translate them into Bash commands just by replacing parentheses with square brackets!

又来强调一遍， `[` 是一个命令。 它不是位于 `if` 和某种类似 `C` 的 “condition” 之间的句法标记。 它也不用于分组。 您不能通过仅仅将括号替换为方括号，来把类似 `C` 的 `if` 命令，转换为 Bash 命令。

如果要表达复合条件，请按照下面的方式做：

```shell
if [ a = b ] && [ c = d ]; then ...
```

请注意，这里在 `if` 之后有两个命令，由 `&&`（逻辑与，快捷求值）运算符连接。 它与以下内容完全相同：

```shell
if test a = b && test c = d; then ...
```

如果第一个测试命令返回 `false`，则不输入 `if` 语句的主体。 如果返回 `true`，则运行第二个测试命令； 如果第二个也返回 `true`，则将输入 `if` 语句的主体。
（C 程序员已经熟悉 `&&` 。Bash 使用相同的短路评估。同样 `||` 对 `OR` 操作进行短路评估。）（注：短路评估英文为 short-circuit evaluation）

`[[` 关键字允许使用 `&&`，所以也可以这样写：

```shell
if [[ a = b && c = d ]]; then ...
```

有关与条件运算符结合的测试的陷阱，请参阅 pitfall #6。

## 拓展阅读

[\[\[](http://mywiki.wooledge.org/BashFAQ/031)

[Bash Pitfalls 6](http://mywiki.wooledge.org/BashPitfalls#pf6)