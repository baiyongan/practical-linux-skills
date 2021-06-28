# Bash Pitfalls Case 10
## if [bar="\$foo"]; then ...

```shell
[bar="$foo"]     # Wrong!
[ bar="$foo" ]   # Still wrong!
[bar = "$foo"]   # Also wrong!
[[bar="$foo"]]   # Wrong again!
[[ bar="$foo" ]] # Guess what?  Wrong!
[[bar = "$foo"]] # Do I really need to say it....
```

正如在前面的例子中所解释的，`[` 是一个命令（可以用类型 `-t [` 或 `whence -v [` 来证明）。 就像任何其他简单的命令一样，Bash 期望命令后跟一个空格，然后是第一个参数，然后是另一个空格，等等。你不能在不输入空格的情况下一起运行所有东西！ 以下才是正确的方法：

```shell
if [ bar = "$foo" ]; then ...

if [[ bar = "$foo" ]]; then ...
```

在第一种形式中，`[` 是命令名称，而 `bar`、`=`、`“$foo”` 的扩展和 `]` 是它的单独参数。 每对参数之间必须有空格，因此 shell 得以知道每个参数的开始和结束位置。 第二种形式类似，除了 `[[` 是一个特殊的关键字，以 `]]` 结尾。 有关两者之间差异的更多详细信息，请参阅 [Bash FAQ 31](http://mywiki.wooledge.org/BashFAQ/031)。

## 拓展阅读

[arguments](http://mywiki.wooledge.org/Arguments)