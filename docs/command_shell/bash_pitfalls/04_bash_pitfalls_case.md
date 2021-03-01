# Bash Pitfalls Case 04
## [ $foo = "bar" ]

这与陷阱2中的问题非常相似，但我重复一遍，因为它很重要。 在上面的示例中，引号放在错误的位置。 你无需用 bash 引用字符串文字（除非它包含元字符或模式字符）。 但是，如果不确定变量是否会包含空格或通配符，则应加引号。

This example can break for several reasons:
此示例可能因以下几个原因而中断：

- 如果 **[** 中引用的变量不存在或为空，则 **[** 命令将最终看起来像：

```shell
[ = "bar" ] # Wrong!
```

...并会引发错误：unary operator expected。 （ = 运算符是二进制的，而不是一元的，因此 [ 命令会让你很惊讶地看到它报如此错误。

- 如果变量包含内部空格，则在 [ 命令运行到它之前，它会被分成单独的单词。 因此：

```shell
[ multiple words here = "bar" ]
```

尽管这对你来说似乎不错，但就 [ 而言，这是一个语法错误。 正确的写法是：

```shell
# POSIX
[ "$foo" = bar ] # Right!
```

即使 $foo 以 - 开头，这在符合 POSIX 的实现上也能正常工作，因为 POSIX [ 取决于传递给它的参数数量，来确定其动作。 只有非常老的 shell 对此有问题，在编写新代码时，你不必担心它们（请参见下面的 x"$ foo" 解决方法）。

在 Bash 和许多其他类似 ksh 的 shell 中，有一个更好的选择，即使用 [[ 关键字。

```shell
# Bash / Ksh
[[ $foo == bar ]] # Right!
```

您无需在 \[\[\]\] 中的 = 的左侧引用变量引用，因为它们不会进行分词或匹配，甚至空白变量也能得到正确处理。另一方面，引用它们也不会造成任何伤害。与 [ 和 test 不同，您还可以使用等价的 ==。但是请注意，使用 [[ 进行比较时，将对右侧的字符串进行模式匹配，而不仅仅是普通的字符串比较。 为了使字符串成为正确的文字，如果使用了在模式匹配上下文中具有特殊含义的任何字符，则必须将其用引号引住。

```shell
# Bash / Ksh
match=b*r
[[ $foo == "$match" ]] # Good! Unquoted would also match against the pattern b*r.
```

你可能已经看过这样的代码：

```shell
# POSIX / Bourne
[ x"$foo" = xbar ] # Ok, but usually unnecessary.
```

x"$foo" hack是必需的，这些代码必须在非常古老的shell上运行，这些shell缺少 [[，并且具有更原始的 [，如果 $ foo 以 - 开头，则会引起混淆。 在上述较旧的系统上，[ 仍然不在乎 = 右侧的标记是否以 - 开头。 它只是按字面意义使用它。 只是左侧需要格外小心。

请注意，需要此解决方法的 shell 并不符合 POSIX。 甚至是 Heirloom Bourne shell 也不需要这样做（可能是非POSIX Bourne shell clone，仍被最广泛地用作系统 shell ）。 这种极端的可移植性很少是必需的，并且会使你的代码可读性更差（更难看）。

## 拓展阅读
[\[\[ keyword](http://mywiki.wooledge.org/BashFAQ/031)