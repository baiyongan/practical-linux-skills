# Bash Pitfalls Case 14
## echo \$foo

这个相对平常的命令易引起巨大的混乱。 因为 `$foo` 没有被引用，所以它不仅会受到 `WordSplitting` 的影响，还会受到 `file globbing` 的影响。 这会误导 Bash 程序员认为他们的变量包含错误的值，而实际上这些变量是可以的——只是 word splitting 或 filename expansion 弄乱了他们对正在发生的事情的看法。

```shell
 msg="Please enter a file name of the form *.zip"
 echo $msg
```

这个 msg 会被拆分为单词，并且任何 glob 都被扩展，例如 *.zip。 你的用户看到这个 msg 时会怎么想：

```shell
 Please enter a file name of the form freenfss.zip lw35nfss.zip
```

展示以下:

```shell
 var="*.zip"   # var 包含一个星号、一个句点和“zip”这个词
 echo "$var"   # 打印出 *.zip
 echo $var     # 打印出以 .zip 结尾的文件列表
```

事实上，`echo` 命令在这里不能绝对安全地使用。 例如，如果变量包含 -n，`echo` 将认为这是一个选项，而不是要打印的数据。 打印变量值的唯一绝对可靠的方法是使用 `printf`：

```shell
 printf "%s\n" "$foo"
```
