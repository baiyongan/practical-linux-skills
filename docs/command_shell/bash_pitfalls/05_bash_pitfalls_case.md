# Bash Pitfalls Case 05
## cd \$(dirname "$f")

This is yet another quoting error. As with a variable expansion, the result of a CommandSubstitution undergoes WordSplitting and pathname expansion. So you should quote it:
这是另一个引用错误。 与变量扩展一样，CommandSubstitution 的结果，也会经历单词拆分和路径名扩展。 所以你应该用引号括住它：

```shell
cd -P -- "$(dirname -- "$f")"
```

这里不明显的是引号如何嵌套。 阅读此内容的C程序员，会将第一个双引号和第二个双引号组合在一起； 然后是第三和第四个。 但是，在Bash中并非如此。 Bash将命令替换内的双引号视为一对，并将替换外的双引号视为另一对。

另一种编写的方式：解析器将命令替换视为“嵌套级别”，并且其内部的引用与外部的引用是分开的。

## 拓展阅读
[CommandSubstitution](http://mywiki.wooledge.org/CommandSubstitution)

[pathname expansion](http://mywiki.wooledge.org/glob)

[quotes](http://mywiki.wooledge.org/Quotes)