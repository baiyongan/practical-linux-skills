# Bash Pitfalls Case 09
## if [grep foo myfile]

许多初学者对 `if` 语句的直觉是错误的，因为看到 `if` 关键字后紧跟 `[` 或 `[[` 的非常常见的模式。
这使人们相信 `[` 不知何故，就该是 `if` 语句语法的一部分，就像 C 的 `if` 语句中使用的括号一样。

不是这种情况！ `if` 接受一个命令。 `[` 是一个命令，而不是 `if` 语句的语法标记。 它等同于 `test` 命令，当然，`[` 最后一个参数必须是一个 `]`。 例如：

```shell
# POSIX
if [ false ]; then echo "HELP"; fi
if test false; then echo "HELP"; fi
```

是等价的。——两者都检查参数“false”是否为非空。 在这两种情况下，总是会打印 HELP，这让其他语言的程序员对 shell 语法的猜测感到惊讶。

`if` 语句的语法是：

```shell
if COMMANDS
then <COMMANDS>
elif <COMMANDS> # optional
then <COMMANDS>
else <COMMANDS> # optional
fi # required
```

再一次，`[` 是一个命令。 它像任何其他常规简单命令一样接受参数。 `if` 是一个包含其他命令的复合命令——并且它的语法中没有 `[` ！

虽然 bash 有一个内置命令 `[` ，但应该知道 `[` 与 `]` 没有什么特别关系。 Bash 只将 `]` 作为参数传递给 `[` 命令，这里要求 `]` 作为最后一个参数，只是为了让脚本看起来更好。

可能有零个或多个可选的 `elif` 部分和一个可选的 `else` 部分。

`if` 复合命令由包含命令列表的两个或多个部分组成，每个部分由 `then`、`elif` 或 `else` 关键字分隔，并由 `fi` 关键字终止。第一部分和每个后续 `elif` 部分的最终命令的退出状态，则会决定是否继续每个相应的 `then` 部分。程序将对另一个 `elif` 做出评估，直到执行其中一个 `then` 部分。 如果没有执行 `then` 部分，则采用 `else` 分支，或者如果没有给出 `else`，则 `if` 块完成并且整个 `if` 命令返回 `0 (true)`。

如果您想根据 `grep` 命令的输出做出决定，你不应将它括在括号、方括号、反引号或任何其他语法中！ 只需在 `if` 之后使用 `grep` 作为命令，如下所示：

```shell
if grep -q fooregex myfile; then
...
fi
```

如果 `grep` 匹配 myfile 中的一行，则退出代码将为 0（真），然后将执行 `then` 部分。 否则，如果没有匹配项，`grep` 将返回非零值，并且整个 `if` 命令将为零。

## 拓展阅读

[BashGuide/TestsAndConditionals](http://mywiki.wooledge.org/BashGuide/TestsAndConditionals)

[http://wiki.bash-hackers.org/syntax/ccmd/if_clause](http://wiki.bash-hackers.org/syntax/ccmd/if_clause)


