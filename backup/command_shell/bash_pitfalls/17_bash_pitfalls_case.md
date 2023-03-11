# Bash Pitfalls Case 17
## echo <<EOF

Here 文档是在脚本中嵌入大块文本数据的有用工具。 它导致将脚本中的文本行重定向到命令的标准输入。 不幸的是，echo 不是从标准输入读取的命令。

```shell
  # 错误写法:
  echo <<EOF
  Hello world
  How's it going?
  EOF

  # 下面才是正确写法:
  cat <<EOF
  Hello world
  How's it going?
  EOF

  # 或者，使用可以跨越多行的引号（高效，echo 是内置的）：
  echo "Hello world
  How's it going?"
```

使用这样的引号很好——它在所有 shell 中都很好用——但它不允许你只将一行行放入脚本中。 第一行和最后一行有句法标记。如果你想让你的行不受 shell 语法的影响，并且不想产生 cat 命令，这是另一种选择：

```shell
  # 或者使用 printf（也高效，printf 是内置的）：
  printf %s "\
  Hello world
  How's it going?
  "
```

在 `printf` 示例中，第一行的 `\` 防止在文本块的开头出现额外的换行符。最后有一个文字换行符（因为最后的引号在新行上）。`printf` 格式参数中缺少 `\n` 会阻止 `printf` 在末尾添加额外的换行符。 `\` 技巧在单引号中不起作用。如果你需要/想要在文本块周围使用单引号，你有两种选择，这两种选择都需要 shell 语法“污染”你的数据：

```shell
  printf %s \
  'Hello world
  '

  printf %s 'Hello world
  '
```
