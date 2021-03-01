# Bash Pitfalls Case 01
## for f in $(ls *.mp3)

[BASH][1] 程序员最常见的错误之一是编写如下循环：

```shell
for f in $(ls *.mp3); do    # Wrong!
    some command $f         # Wrong!
done

for f in $(ls)              # Wrong!
for f in `ls`               # Wrong!

for f in $(find . -type f)  # Wrong!
for f in `find . -type f`   # Wrong!

files=($(find . -type f))   # Wrong!
for f in ${files[@]}        # Wrong!
```

是的，如果你能够仅将 ls 或 find 的输出，做为文件名列表并对其进行迭代，那自然是极好的。 但是你不能。 整个方法都存在着致命的缺陷，并且没有什么技巧能使它起作用。 你必须使用完全不同的方法。

以上这种写法，至少有6个问题：

1. 如果文件名包含空格（或 $IFS 当前值中的任何字符），它将会被 [WordSplitting][2]。 假设我们在当前目录中有一个名为 01 - Don't Eat the Yellow Snow.mp3 的文件，则for循环将遍历结果文件名中的每个单词：01, -, Don't, Eat, 等等。

2. 如果文件名包含[glob][3]字符，则会对其进行文件名扩展（“[globbing][4]”）。 如果 ls 有任何包含*字符的输出，则包含该字符的单词将被识别为一种匹配模式，并替换为与之匹配的所有文件名的列表。

3. 如果命令替换返回多个文件名，则无法分辨第一个文件在哪里结束，第二个文件在哪里开始。 路径名可以包含除NUL之外的任何字符。 是的，这其中也包括换行符。

4. ls 这个实用程序可能会破坏文件名。 根据你所使用的平台，所使用（或未使用）的参数，以及其标准输出是否指向终端，ls可能会随机决定将文件名中的某些字符替换为“？”， 或根本不打印它们。 [切勿尝试解析ls的输出][5]。 ls 操作完全没有必要。 这是一个外部命令，其输出专门供人类读取，而不是由脚本解析。

5. [CommandSubstitution][6] 从其输出中删除所有结尾的换行符。 由于ls添加了换行符，所以这似乎是合乎需要的，但是如果列表中的最后一个文件名以换行符结尾，则`...`或$（）也会删除该行。

6. 在ls示例中，如果第一个文件名以连字符开头，则可能导致[陷阱3][7]。

你也不能简单地用双引号代替：

```shell
for f in "$(ls *.mp3)"; do     # Wrong!
```

这使得ls的整个输出被视为一个单词。 循环只执行一次，而不是遍历每个文件名，而是将所有文件名都绑在一起的字符串分配给f。

你也不能简单地将[IFS][8]更改为换行符。 文件名也可以包含换行符。

此主题的另一个变体，是滥用分词和for循环以（错误地）读取文件的行。 例如：

```shell
IFS=$'\n'
for line in $(cat file); do ...     # Wrong!
```

[这样是行不通的！][9] 特别是当如果这些行是文件名时。 Bash（或任何其他Bourne家族的shell）无法通过这种方式运行。
那么，正确的方法是怎样做呢？

有一下几种方法，这主要取决于你是否需要递归扩展。

如果不需要递归，则可以使用一个简单的[glob][10]，代替ls的操作：

```shell
for file in ./*.mp3; do    # Better! and...
    some command "$file"   # ...always double-quote expansions!
done
```

POSIX shell程序（例如Bash）具有专门用于此目的的globing功能——允许shell程序，将模式扩展为匹配文件名的列表。 无需解析外部实用程序的结果。 因为globing是最后一个扩展步骤，所以./*.mp3模式的每个匹配项，都正确地扩展为一个单独的单词，并且不受未引用扩展的影响。

Question: What happens if there are no *.mp3-files in the current directory? Then the for loop is executed once, with file="./*.mp3", which is not the expected behavior! The workaround is to test whether there is a matching file:

问题：如果当前目录中没有* .mp3-文件，该怎么办？ 那么，将会以 file=“ ./*.mp3” 为循环条件， 执行一次for循环，这不是预期的行为！ 解决方法是测试是否有匹配的文件：

```shell
# POSIX
for file in ./*.mp3; do
    [ -e "$file" ] || continue
    some command "$file"
done
```

另一个解决方案是使用Bash的shopt -s nullglob功能，然而，仅应当在阅读文档，并仔细考虑此设置对脚本中所有其他glob的影响之后，才建议使用。

如果您需要递归，则标准解决方案是使用 find 命令。 [使用 find][11] 时，请确保正确地使用它。 为了实现POSIX sh的可移植性，请使用-exec选项：

```shell
find . -type f -name '*.mp3' -exec some command {} \;

# Or, if the command accepts multiple input filenames:

find . -type f -name '*.mp3' -exec some command {} +
```

如果您使用的是bash，则还有两个额外的选项。 一种是使用GNU或BSD find 的 -print0 选项，以及bash的read -d '' 选项和 [ProcessSubstitution][12]：

```shell
while IFS= read -r -d '' file; do
  some command "$file"
done < <(find . -type f -name '*.mp3' -print0)
```

这样做的好处是，“some command”（实际上是整个while循环主体）会在当前shell中执行。 你可以设置变量，并使它们[在循环结束后保持不变][13]。

在[Bash 4.0及更高版本][14]中，可用的另一个选项是globstar，它允许递归扩展glob：

```shell
shopt -s globstar
for file in ./**/*.mp3; do
  some command "$file"
done
```

注意上面示例中 $file 的双引号。 这导致了我们的第二个bash陷阱。

## 拓展阅读

[1]: http://mywiki.wooledge.org/BASH
[2]: http://mywiki.wooledge.org/WordSplitting
[3]: http://mywiki.wooledge.org/glob
[4]: http://mywiki.wooledge.org/glob
[5]: http://mywiki.wooledge.org/ParsingLs
[6]: http://mywiki.wooledge.org/CommandSubstitution
[7]: http://mywiki.wooledge.org/BashPitfalls#pf3
[8]: http://mywiki.wooledge.org/IFS
[9]: http://mywiki.wooledge.org/DontReadLinesWithFor
[10]: http://mywiki.wooledge.org/glob
[11]: http://mywiki.wooledge.org/UsingFind
[12]: http://mywiki.wooledge.org/ProcessSubstitution
[13]: http://mywiki.wooledge.org/BashFAQ/024
[14]: http://mywiki.wooledge.org/BashFAQ/061