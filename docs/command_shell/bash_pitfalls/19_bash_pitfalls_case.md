# Bash Pitfalls Case 19
## cd /foo; bar

如果不检查 `cd` 命令中的错误，就可能最终会在错误的位置执行 `bar`。 这可能是一场重大灾难，例如，如果 `bar` 恰好是 `rm -f *`。

因此必须始终检查 `cd` 命令中的错误。 最简单的方法是：

```shell
 cd /foo && bar
```

如果 cd 后有多个命令，你可能更喜欢这样：

```shell
 cd /foo || exit 1
 bar
 baz
 bat ... # 很多命令
```

`cd` 将报告更改目录失败，并带有 `stderr` 消息，例如 `“bash: cd: /foo: No such file or directory”`。 但是，如果想在标准输出中添加自己的消息，则可以使用命令分组：

```shell
 cd /net || { echo >&2 "Can't read /net. Make sure you've logged in to the Samba network, and try again."; exit 1; }
 do_stuff
 more_stuff
```

请注意， `{` 和 `echo` 之间有一个必需的空格，并且在 `}` 结束之前, 必需有一个 `;`。 也可以编写一个 die 函数。

有些人还喜欢启用 `set -e` 使他们的脚本在任何返回非零的命令上中止，但这可能很难正确地使用（因为许多常见的命令，可能会在仅是警告的情况下，便返回非零值，而并非我们认为的 fatal error）。

By the way, if you're changing directories a lot in a Bash script, be sure to read the Bash help on pushd, popd, and dirs. Perhaps all that code you wrote to manage cd's and pwd's is completely unnecessary.

顺便说一句，如果你在 Bash 脚本中频繁更改目录，请务必阅读有关 pushd、popd 和 dirs 的 Bash 帮助。 也许您为管理 cd 和 pwd 而编写的所有代码都是完全没有必要的。

对比一下如下两种写法：

```shell
 find ... -type d -print0 | while IFS= read -r -d '' subdir; do
   here=$PWD
   cd "$subdir" && whatever && ...
   cd "$here"
 done
```

```shell
 find ... -type d -print0 | while IFS= read -r -d '' subdir; do
   (cd "$subdir" || exit; whatever; ...)
 done
```

在此处强制使用 SubShell 会使得 `cd` 仅出现在 subshell 中； 对于循环的下一次迭代，无论 `cd` 是成功还是失败，我们都会回到正常位置。 我们不必手动改回来，也不会陷入无休止的 `... && ...` 逻辑字符串中，而阻止使用其他条件。 subshell 版本更简单、更干净（虽然慢了一点）。

另一种方法是在每次循环迭代开始时无条件地 `cd` 到我们应该在的位置：

```shell
 here=$PWD
 find ... -type d -print0 | while IFS= read -r -d '' subdir; do
    cd "$here" || continue
    cd "$subdir" || continue
    whatever
    ...
 done
```
至少这样，我们可以继续下一次循环迭代，而不必将一串不确定的 `&&` 串在一起，以确保我们到达循环体末尾的 `cd`。


