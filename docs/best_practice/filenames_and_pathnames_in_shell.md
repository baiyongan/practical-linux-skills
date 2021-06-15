# Shell 中的文件名与路径名：如何正确地设置

许多Bourne Shell脚本(比如由bash，dash，ash，ksh等运行)在类 Unix/POSIX 的系统上，并不能正确处理文件名和路径名。一些 shell 编程书籍的教法是错的，甚至POSIX标准有时也把它弄错了。因此，许多shell脚本都是错误的，会导致意外的失败，并在某些情况下会导致安全漏洞。这是一个真正的问题，因为在类 Unix 系统(例如Unix，Linux或POSIX)上，shell是通用的，并且广泛用于许多基本任务。

本文首先总结了正确处理文件名的一些基本原则。然后，通过在 Bourne shell 中处理文件名和路径名的常见错误方法这一节，来证明这些理由。接着，历数其中的基本原理，从而使你可以了解——为什么通用技术不起作用...以及替代方案为什么能起作用。前提假设你已经知道如何编写Bourne Shell脚本。

基本的问题是，当今大多数类Unix都允许文件名几乎包含所有字节。其中包括换行符，制表符，转义字符(包括在显示时可以执行命令的转义序列)，其他控制字符，空格(任何位置都可能有！)，前划线(-)，shell元字符和不是合法UTF-8 编码的字符串。因此，如果在脚本执行中，你去解压其他人的 "tar" 或 "zip" 文件；或是检查包含其他人创建的文件的目录时，又或仅仅是自己创建包含 shell 元字符(例如空格或问号)的文件时，脚本都可能会运行失败甚至被破坏。

这不仅仅是 shell 中会有的问题。 所有语言(不仅是 shell 程序)中的许多代码，以及至少某些 GUI 工具包，都无法正确地处理所有允许存在的文件名和路径名。某些GUI工具箱(例如 file-pickers) 假定文件名始终为UTF-8，并且从不包含控制字符，虽然名字中包含它们的做法，不一定都是正确的。

但是，类Unix内核中的此缺陷(即允许使用危险的文件名)与 Bourne Shell 语言中的其他弱点相结合，使得 Shell 在正确处理文件名和路径名时更加困难。对于简短的脚本来说，使用 shell 是一种合适的方式。但是其对于文件名的过度宽容，会使得简单的任务变得容易出错。进行一些小的更改，将更加容易地编写出用于处理(包括 shell 程序在内的所有语言)文件名的安全代码。因此，如果你的脚本需要处理未存档的文件，或由其他用户或移动应用程序创建的文件，那么，你的脚本就需要应对这种糟糕的情况。诸如 shellcheck 之类的工具，可以帮助你发现其中一些问题，但并非全部。如果你了解问题，则可以更有效地使用这些工具。

首先，一些关键术语。
**路径名(pathname)**，用于标识特定文件，并且可以包含零个或多个 "/" 字符。每个**路径名组件(pathname component)**(由"/"分隔)是一个正式名称，即**文件名(filename)**；路径名组件(即文件名)不能包含 "/"。

因此，准确来说，"/usr/bin/sh" 是一个路径名，其中包含路径名组件(文件名)，它指向一个特定的文件。(注意：在Cygwin上，"\" 是 "/" 的同义词，因此它也分隔路径名组件。)实际上，许多人使用术语"文件名"来表示路径名组件(正式来说是文件名)和整个路径名。路径名组件和完整路径名都不能包含NUL字符(\0)，因为那是终止符，并且路径名组件也不能包含 "/"。

# 1 正确操作：快速总结

那么，如何在shell中正确处理路径名呢? 以下是简要说明。

## 1.1 基本原则

1. 将所有变量引用和命令替换都用双引号引起来，除非你能确定它们只包含字母数字字符，或你有特殊准备的内容(即，使用 `"$variable"` 而不是 `$variable`)。尤其是，你实际上应该始终将 `$@` 放在双引号内； POSIX 将其定义为特殊情况(即使它在双引号内，它也会作为单独的字段扩展到位置参数中)。

2. 如果可以的话，将 IFS 设置为仅换行符和制表符，以减少对文件名使用空格进行错误处理的风险。使用换行符或制表符，来分隔存储在单个变量中的选项。 使用 `IFS ="$(printf '\n\t')"` 设置 IFS

3. 给所有路径名前缀加上前缀，以使它们不能以 `"-"` 开头。特别是，切勿以 `"?"` 开头 或 `"*"` (例如 `"*.pdf"`)；始终在 glob 前面添加一些无法扩展为破折号的内容(例如`"./"`)。因此，切勿使用 `"*.pdf"` 之类的模式； 使用 `"./*.pdf"` 代替。

4. 接受路径名时，请检查路径名是否以 `"-"` 开头，然后添加 `"./"`。

5. 显示或存储路径名时要小心，因为它们可能包括换行符、制表符、终端控制转义序列、非 UTF-8 字符(或不在你的语言环境中的字符)等。你可以使用如下方式，以在显示之前，去除控制字符和非 UTF-8 字符。
```shell
printf '%s' "$file" | LC_ALL=POSIX tr -d '[:cntrl:]' | iconv -cs -f UTF-8 -t UTF-8
```

6. 不要依赖于始终在选项和路径名之间使用 `"--"`，来作为针对以 `"-"` 开头的文件名的主要对策。你必须对每个命令都这样做才能使其工作，但人们不会一直使用它(他们从来没有)，而且许多程序(包括 echo)不支持 `"--"`。可以在选项和路径名之间使用 `"--"`，但仅作为额外的可选保护措施。

7. 使用已知的可以正常工作的模板； 下面会介绍一些经过测试的模板。还没有标准的方法来做到这一点(就目前而言)。

8. 考虑禁用带有前导破折号的 glob 匹配。如前所述，始终在前面加上 globs (例如，使用`"./"`)，因此你永远不会有一个带有前导 `"-"` 的 glob。还可以考虑禁用前导破折号匹配，这样如果你不小心忘记这样做，该错误就不太可能成为安全漏洞。在 bash 中，你可以设置 ` GLOBIGNORE='-*:.:..'`; 注意 `-*`。   oil shell 最近添加了 `shopt -u dashglob` 以禁用返回前导破折号的 globs (此命名与破折号中的 dotglob 一致)。然后 globs 将不再返回以裸破折号开头的任何内容。 通常明智的做法是，设置你的系统，以限制不可避免的错误造成的损害。

9. 考虑对 shell 使用非官方的严格模式。 这不是特定于文件名，但总体上是个好主意。 `"set -eu"` 适用于所有 POSIX shell。可以参考文章 Use Bash Strict Mode (Unless You Love Debugging) ，其写法如下：

```shell
    #!/bin/bash
    set -euo pipefail
    IFS=$'\n\t'
```

10. 使用 shellcheck 之类的工具来查找你遗漏的问题。

## 1.2 模板：使用 globs

```shell
 # 正确的便捷的 glob 使用： 使用 "for" 循环，前缀有 glob， 检查是否存在：
 # （请记住，globs 匹配通常不包含以“.”开头的文件）
 for file in ./* ; do        # 以“./*”为前缀，切勿仅仅以“*”开头
   if [ -e "$file" ] ; then  # 确保它不是空匹配
     COMMAND ... "$file" ...
   fi
 done
```

```shell
 # 正确的便捷的 glob 使用，包括隐藏文件（以“.”开头）：
 for file in ./* ./.[!.]* ./..?* ; do        # 以 "./*" 为前缀
   if [ -e "$file" ] ; then  # 保证不是空匹配
     COMMAND ... "$file" ...
   fi
 done
```

```shell
 #正确的 glob 使用，更简单但需要非标准的 bash 扩展 nullglob：
 shopt -s nullglob  # Bash 扩展，因此没有匹配项的 glob 返回空
 for file in ./* ; do        # 使用“./*”，永远不要直接使用“*”
   COMMAND ... "$file" ...
 done
```

```shell
 # 正确的 glob 使用，更简单但需要非标准的 bash 扩展 nullglob；
 # 如果您可以添加 /dev/null 作为输入，您可以在一行上做一些事情。
 shopt -s nullglob  # Bash 扩展，因此没有匹配项的 glob 返回空
 COMMAND ... ./* /dev/null
```

## 1.3 模板：使用 find

find 命令非常适合用于递归处理目录。 通常，您会指定要 find 的其他参数（例如，使用 “-type f” 仅选择普通文件）。 例如，以下是使用 find 遍历文件系统、跳过所有 “隐藏” 目录和文件（名称以“.”开头）并仅处理以 `.c` 或 `.h` 结尾的文件的示例：

```shell
  find . \( -path '*/.*' -prune -o ! -name '.*' \) -a -name '*.[ch]'
```

以下是始终有效的形式（尽管有些需要非标准扩展或在 Cygwin 中失败），其次是具有严格限制的更简单的形式。

### 1.3.1 始终有效

```shell
 # 简单的 find -exec; 如果 COMMAND 很大，则会很笨拙，并为每个文件创建一个进程：
find . -exec COMMAND... {} \;
```

```shell
# 简单的 find -exec 带上 +，如果多个文件都可以用于 COMMAND，则执行更快：
 find . -exec COMMAND... {} \+
```

```shell
# 使用带有 \0 分隔符的 find 和 xargs
#（非标准通用扩展 -print0 和 -0。适用于 GNU、*BSD、busybox）
 find . -print0 | xargs -0 COMMAND
```

```shell
# 令人头疼，但它可以移植。 在命令中使用 '\'' 作为单引号。
  # 运行一个子shell，所以每次迭代后变量值都会丢失：
 find . -exec sh -c '
  for file do
     ...  # Use "$file" not $file
  done' sh {} +
```

```shell
 # find... while loop, requires find (-print0) and shell (read -d) extensions.
 # Fails on Cygwin; in while loops, filenames ending in \r \n and \n look =.
 # Variable values may be lost unset because loop may run in a subshell.
 find . -print0 | while IFS="" read -r -d "" file ; do ...
   COMMAND "$file" # Use quoted "$file", not $file, everywhere.
 done
```

```shell
 # while + find with process substitution.
 # Requires nonstandard read -d (bash ok) and find -print0.
 # Rquires nonstandard process redirection <(...); bash, zsh, and ksh 93
 # have process redirection, but dash and ksh 88 do not. Also,
 # the underlying system must have named pipes (FIFOs) or /dev/fd.
 # Fails on Cygwin; in while loops, filenames ending in \r \n and \n look =.
 # Variables *do* retain their value after the loop ends, and
 # you can read from stdin (change the 4s to another number if fd 4 is needed):
 while IFS="" read -r -d "" file <&4 ; do
   COMMAND "$file" # Use quoted "$file", not $file, everywhere.
 done 4< <(find . -print0)
```

```shell
 # Named pipe version.
 # Requires nonstandard extensions to find (-print0) and read -d (bash ok);
 # underlying system must inc. named pipes (FIFOs).
 # Fails on Cygwin; in while loops, filenames ending in \r \n and \n look =.
 # Variables *do* retain their value after the loop ends, and
 # you can read from stdin (change the 4s to something else if fd 4 needed).
 mkfifo mypipe
 find . -print0 > mypipe &
 while IFS="" read -r -d "" file <&4 ; do
   COMMAND "$file" # Use quoted "$file", not $file, everywhere.
 done 4< mypipe
```

```shell
 # Use the author's encodef program.
 # Variables *do* retain their value after the loop ends.
 # This version is POSIX portable, but you must have encodef. In practice
 # you can often use "-print0" instead of POSIX "-exec printf '%s\0' {} \;"
 for encoded_pathname in $(find . -exec printf '%s\0' {} \; | encodef ) ; do
   file="$(encodef -dY -- "$encoded_pathname")" ; file="${file%Y}"
   COMMAND "$file" # Use quoted "$file", not $file, everywhere.
 done
```

### 1.3.2 局限性

有时不完全处理路径名会更容易，尤其是当您尝试编写可移植的 shell 代码时。但是，如果您使用该代码来检查扩展档案（例如 zip 或 tar 文件），或检查包含由其他人创建的文件的目录（例如，远程文件系统、由其他人或攻击者控制的虚拟机、另一个移动应用程序等），该代码很快就会成为安全漏洞）。以下是示例（及其限制）：

```shell
 # Okay if pathnames can't contain tabs or newlines; beware the assumption:
 IFS="$(printf '\n\t')"
 set -f # Needed for filenames with *, etc; see below on saving/restoring
 for file in $(find .) ; do
   COMMAND "$file" ...
 done
```

```shell
 # Okay if pathnames can't contain tabs or newlines; beware the assumption:
 IFS="$(printf '\n\t')"
 set -f # Needed for filenames with *, etc; see below on saving/restoring
 COMMAND $(find .) /dev/null
```

```shell
 # Okay if pathnames can't contain newlines; beware the assumption.
 # Also, this makes stdin inaccessible, and variables may not stay set.
 find . | while IFS="" read -r file ; do ...
   COMMAND "$file" # Use "$file" not $file everywhere.
 done
```

```shell
 # You can securely use the above approaches, even if directories have
 # evil filenames, if you can skip evil filenames.   For example, here's how to
 # skip pathnames with embedded control chars, including newline and tab:
 IFS="$(printf '\n\t')"
 controlchars="$(printf '*[\001-\037\177]*')"
 set -f # Needed for filenames with *, etc; see below on saving/restoring
 for file in $(find . ! -name "$controlchars") ; do
   COMMAND "$file" ...
 done
```

```shell
 # Skip pathnames with embedded control chars, including newline and tab:
 IFS="$(printf '\n\t')"
 controlchars="$(printf '*[\001-\037\177]*')"
 set -f # Needed for filenames with *, etc; see below on saving/restoring
 COMMAND $(find . ! -name "$controlchars") /dev/null
```

```shell
 # Here's one way to quickly exit a program if a filename
 # contains a control character (including tabs, newlines, and ESC) or DEL.
 # My thanks to Michael Thayer for this suggestion.
 expr "$filename" : "`printf '.*[\01-\037\0177*?]'`" && exit 1
```

## 1.4 模板：设置一个变量

没有简单的可移植方法来处理一个变量中的多个任意文件名，然后直接使用它们。Shell 数组可以工作，但在这种情况下使用起来可能很棘手，而且不可移植。我建议禁止带有制表符和换行符的文件名； 那么您可以轻松地将这些字符用作分隔符，如下所示：

```shell
# 如果在字符串中建立选项，请使用制表符|换行符分隔文件名
 IFS="$(printf '\n\t')"
 tab="$(printf '\t')"
 command_options="-x${tab}-y"
# 如果你想把路径名放在内置字符串中，在路径名中防止出现 tab|newline，使用“set -f”，然后你可以使用一个不带引号的变量。
# 例如，假设 $file 不包含 tab|newline，-F $file 是：
 command_options="${options}-F${tab}${file}"
 set -f # 需要带 * 等的文件名； 有关保存/恢复的信息，请参见下文 1.5 章节
 mycommand $command_options "$another_pathname"
```

## 1.5 模板：保存和还原 "set -f"

有时您需要在 shell 中禁用 globbing，尤其是在从 find 接收信息时。POSIX 包括各种便捷的机制，来禁用和重新启用 shell 中的 globbing。“set -f”命令禁用文件通配。 您可以使用“set -f”禁用文件通配，并使用“set +f”重新启用它。 但是，如果您想使用“set -f”暂时禁用文件通配，然后恢复之前的所有内容，该怎么办？ 一种方法是将“set -f”及其所依赖的内容放在子shell中； 这样可行，但是一旦子shell完成，变量设置就会丢失。 您还可以通过执行以下操作来保存和恢复 shell 选项设置：

```shell
 oldSetOptions=$(set +o)             # 保存 shell 选项设置
 ... (set -f, etc.)
 eval "$oldSetOptions" 2> /dev/null  # 恢复 shell 选项设置
```

# 2 错误操作

但是为什么你需要遵守这些规则呢？ 找出问题的最简单方法是查看一些错误的示例，因为要真正了解如何修复问题，您需要知道哪里出了问题。这些示例假定默认设置（例如，没有“set -f”或“IFS=...”）：

```shell
cat * > ../collection  # 错误
```

这种方式是错误的。如果当前目录中的文件名以“-”开头，则会被误解为选项而不是文件名。 例如，如果有一个名为“-n”的文件，它会立即启用 cat 的“-n”选项，而不是视作要被 cat 的一个文件（GNU cat 就是这样做，它会为行编号）。 一般来说，你不应该有一个以 `“*”` 开头的glob——它应该以 “./” 为前缀。 此外，如果目录中没有（未隐藏的）文件，则 glob 模式将改为返回模式（`“*”`）； 这意味着命令 (cat) 将尝试打开一个不可能的名为“*”的文件。

```shell
for file in * ; do  # 错误
  cat "$file" >> ../collection
done
```

也是错误的，出于同样的原因； 名为“-n”的文件会欺骗 cat 程序，如果模式不匹配，它将以模式本身作为值循环一次。

```shell
cat $(find . -type f) > ../collection  # 错误
```

错误。 如果任何路径名包含空格、换行符或制表符，则其名称将被拆分（文件“a b”将被错误地解析为“a”和“b”两个文件）。 如果路径名包含像 * 这样的通配符，shell 将尝试扩展它，这可能会产生其他问题。 此外，如果 find 命令不匹配任何文件，则该命令将不带参数运行； 在许多命令（如 cat）上，这将导致程序挂在标准输入的输入上（您可以通过附加路径名 /dev/null 来解决这个问题，但很多人不知道这样做）。

```shell
( for file in $(find . -type f) ; do  # 错误
    cat "$file"
  done ) > ../collection
```

错误，出于类似的原因。 这会分解包含空格、换行符或制表符的路径名，并且如果路径名本身包含诸如“*”之类的字符，则会错误地扩展路径名。

```shell
 ( find . -type f |    
   while read file ; do cat "$file" ; done ) > ../collection # 错误
```

错误。 如果路径名中间有空格，则此方法有效，但如果路径名以空格开头或结尾（它们会被截断），则此方法将无法正常工作。 此外，如果路径名包含`“\”`，它会被破坏； 特别是，如果它以`“\”`结尾，它将与下一个路径名组合（删除两者）。 通常，在 shell 中使用不带`“-r”`选项的“read”通常是错误的，在许多情况下，您应该在读取之前设置 IFS=""。

```shell
( find . -type f | xargs cat ) > ../collection # 错误
```

错误。 默认情况下，xargs 的输入被解析，因此空格字符（以及换行符）分隔参数，反斜杠、单引号、双引号和 & 字符用于引用。 根据 POSIX 标准，您必须考虑到选项 -E "" ，或下划线也可能具有特殊含义。 请注意，POSIX 标准 xargs 部分中的许多示例都是错误的； 带有空格、换行符或许多其他字符的路径名将导致许多示例失败。

```shell
 ( find . -type f |
   while IFS="" read -r file ; do cat "$file" ; done ) \
          > ../collection # 错误
```

错误。 像许多程序一样，这种写法，假设您可以拥有路径名列表，且每行一个路径名。 但是由于路径名可以在内部包含换行符，所以所有简单的对路径名一次一行处理的方式，都是错误的！ 如果路径名不能包含换行符，则这种构造还好，但由于许多类 Unix 系统允许，攻击者很乐意使用此错误假设作为攻击。

```shell
cat $file # 错误
```

错误。 如果 `$file` 包含空格，那么它可以分解并解释为多个文件名，如果 `$file` 以破折号开头，则该名称将被解释为一个选项。 此外，如果 `$file` 包含像 “*” 这样的元字符，它将首先被扩展，产生错误的文件名集。


# 3 基本规则的基本原理

## 3.1 双引号参数(变量)引用和命令替换

As described by any Bourne shell programming book, always use double-quotes (") to surround variable references and command substitutions, unless you are certain they can only produce alphanumeric characters or you have specially prepared things. The dangerous characters are whitespace or shell pathname expansion (glob) characters like "*", because unquoted variable references and command substitutions undergo shell field splitting and pathname expansion:

- Field splitting splits a word into multiple words; by default they are split by space, tab, or newline. This expansion can be controlled or disabled by setting the variable IFS, but you have to specially set it.
- Pathname expansion looks for filename patterns, and if they are found, splits up a word into multiple words for each filename. If the unquoted variable reference or command substitution produces a character like *, shell will normally try to replace that with a list of the filenames that match the pattern. This expansion can be disabled using "set -f".
The good news is that once you get into the habit, this is an easy style rule to follow. Even if you know that they can only produce characters that will not cause problems, quoting is a good idea, since the script might change in the future. It is easy to remember "alphanumeric characters okay" than a more complicated rule, and if you allow more than alphanumeric characters, it is likely that the variable will eventually allow dangerous characters. Lots of scripts already follow this rule, so while it’s annoying, it’s not too bad. Here are some examples:

Don’t use	Instead use
$file	"$file"
$(pwd)	"$(pwd)"
$(dirname $file)	"$(dirname "$file")"

By the way, it turns out that the POSIX spec is unclear whether or not field splitting applies to arithmetic expansion in shell; most (but not all) implementations do apply field splitting in this case.

## 3.2 在每个脚本的开头将 IFS 设置为仅换行符和制表符

One of the first non-comment commands in every shell script should be:

```shell
   IFS="$(printf '\n\t')"
   # or:
   IFS="`printf '\n\t'`"
   # Widely supported, POSIX added http://austingroupbugs.net/view.php?id=249
   IFS=$'\n\t'
```

To understand this recommendation, you need to know what IFS is. IFS is the list of input field separators in shell. The POSIX specification XCU section 2.6.5 explains that after various expansions (such as parameter expansion and command subsitution), the results not in double-quotes are split up where they include input field separators. By default IFS is space, tab, and newline.

This recommendation sets the IFS variable so that the "space" character is no longer an input field separator, and thus only newline and tab are field separators. If you need to run on really old systems the second form with backquotes is better, but the first one is easier to read, POSIX compliant, and very portable - it works on any system not in a museum.

This doesn’t help security very much, but it does help reliability. If you make a mistake in your script, and the script encounters a pathname (or other data) with a space, your script is more likely to work correctly. Filenames with tabs and newlines are almost never used except by attackers, but users often use spaces; doing this will prevent file splitting from unintentionally splitting up filenames with spaces. So if you forget to surround a variable reference with double-quotes, or use a for loop with a simple command substitution, it is less likely to fail. It also makes it possible to combine options and filenames with spaces; you can use filenames with spaces (as usual), and separate the options with tabs (this isn’t a common convention, but I think it’s a reasonable one). It is also really easy to do this; just add one line near the top.

The recommended IFS command sets newline and then tab. It is harder to do it in the other order in some shells, because $(...) consumes trailing newlines. The easy way is to use IFS=$'\t\n', which is widely supported but has only recently been added to POSIX.

You can still build a list of command options inside a single shell variable, even when space isn’t in IFS. You just need to use tab or newline to separate parameters, and not space. You can even embed pathnames with spaces in this variable, since spaces are no longer field separators.

You might also want to put "set -eu" or at least "set -u" at the beginning of your scripts, along with setting IFS. This does nothing for pathnames, but these can help detect other script errors.

By the way, the need for proper quoting is not limited to Bourne shells. The Windows shell also requires proper quoting, and improper quoting can lead to vulnerabilities. A user merely needs to create filenames with characters such as ampersands, and an improperly-quoted shell program might end up running it. For example, imagine if an attacker can create a directory of the form "name&command_to_execute", say on a fileserver. Then a Windows script which fails to quote properly (e.g., it has ECHO %CD% or SET CurrentPath=%CD% without putting double-quotes around %CD%) would end up running the command of the attacker’s choosing.

## 3.3 前缀所有 glob，以便它们无法扩展以"-"开头

A "glob" is a pattern for pathname matching like "*.pdf". Whenever you use globbing to select files, never begin with a globbing character (typically the characters "*", "?", or "["). If you’re starting from the current directory, prefix the glob with "./" like this:

```shell
 cat ./*                   # Use this, NOT "cat *" ... Must have 1+ files.
 for file in ./* ; do      # Use this, NOT "for file in *" (beware empty lists)
   ...
 done
```

This is important because almost all commands will interpret a string beginning with dash as an option, not as a filename, until they see something that does not begin with dash. Globs are expanded by the shell into a list of filenames, and dash is earlier in the sort order compared to before alphanumerics, so it is easy for attackers to make this happen.

If you always prefix pathnames (e.g., those acquired through globs), then pathnames starting with "-" will always be handled correctly. Globbing is often the easiest way to handle all files, or a subset of them, in a specific directory, but you need to make sure you do it correctly.

## 3.4 接受路径名时，请检查路径名是否以"-"开头，然后是否以" ./"开头

Similar to the previous rule, if you read in a pathname, as early as possible see if it begins with "-"... if it does, prepend "./". This eliminates this source of pathnames that are confused as option flags.

## 3.5 在显示或存储路径名时要小心

Filter or encode pathnames before displaying them. The biggest problem is that pathnames could contain control characters that control the terminal and/or the GUI display, causing nasty side-effects on display. Displaying pathnames can even cause a security vulnerability in some situations (!). If you must display pathnames, consider encoding or stripping out control characters first (many ls implementations do this when the output is a terminal). You can strip out the control characters this way:

```shell
 printf '%s' "$file" | LC_ALL=POSIX tr -d '[:cntrl:]'
```

In addition, you have no way of knowing for certain what the pathname’s character encoding is, so if you got a pathname from someone else, and they do not use UTF-8 (including ASCII), you’re likely to end up with garbage mojibake.

In practice, what most people do is exchange pathnames and hope that they are UTF-8. If you both use the same locale, you could use that instead, but UTF-8 is the only encoding in wide use for Unix pathnames that can handle arbitrary languages. Most modern GUI toolkits presume that filenames are UTF-8, even though nothing actually ensures that this is true. If you must display pathnames, consider forcing them to display as UTF-8. I encourage you to always encode pathnames in UTF-8... but beware that nothing actually enforces this common convention. Thus, you will want to enforce it yourself where you can.

One way you can avoid displaying non-UTF-8 filenames in shell is to try to convert them to UTF-8 using iconv. The iconv program is in POSIX, and it can strip out characters not in a given encoding. Sadly, the encodings that must be supported by iconv are not standardized. Still, GNU iconv supports UTF-8, and other systems are likely to do so, so this will probably work:

```shell
  printf '%s' "$file" | iconv -cs -f UTF-8 -t UTF-8
```

A common approach for storing pathnames in files, or to transmit them in data formats, is to separate them with newlines and/or tabs. Sadly, this does not work in the general case, since pathnames can include both characters. You need to forbid such nasty filenames, escape them, or use \0 to separate the pathnames. If you can forbid them, that is the easiest... but you may not have that option.

## 3.6 不要依赖"--"

Many books, and the POSIX standard, mistakenly advocate using "--" between the options and pathnames as the primary method to deal with filenames beginning with "-". This is impractical and bad advice:

1. For "--" to work, all maintainers would have to faithfully use "--" in practically every command invocation. That just doesn’t happen in real life, even after decades of people trying. People forget it all the time; no one is that consistent, especially since code seems to work without it. Very few commands require it, after all.
2. You can’t do it anyway, even if you were perfectly consistent; many programs and commands do not support "--". POSIX even explicitly forbids echo from supporting "--", and echo must support "-n" (and GNU coreutils echo supports other options too).
Thus, as a practical matter you need to do something else; by always prefixing filenames if they start with dash, as recommended earlier, the problem disappears.

Do feel free to use "--" between options and pathnames, when you can do it, as an additional protective measure. But using "--" as your primary (or only) mechanism for dash-prefixed filenames is bad idea. You are better off prefixing the pathnames when you get the pathname, since then you only have to do it once per pathname. Once you prefix the pathname it doesn’t matter if you remember "--" or not; it just works correctly.

## 3.7 使用通配符并适当查找(并处理空匹配项)

There are two major ways to get sets of pathnames in the shell, glob patterns and the find command. Globs are primarily useful for a short list of unhidden filenames in one directory; find is useful for other situations, including recursively descending into subdirectories.

In both cases you have to worry around what happens when there are zero matches. If you just gave "command" and something that gave a list of filenames, most commands will hang while trying to read from standard input. The easy solution in this case is to add "/dev/null" to the end... assuming you can do that.

The next two sections examine glob patterns and the find command in turn.

# 4 Globbing
Globbing is a simple language specifically designed for filename handling, primarily to create lists of unhidden files in a particular directory. In this language, "*" matches all non-hidden files in the current directory, "*.pdf" matches all non-hidden files in the current directory ending in ".pdf" - and so on.

The good news about globbing in shell is that glob expansion is built into the shell and done after field (IFS) expansion. Thus, as long as you directly use globs as command parameters or as part of a "for" loop, you will have no problem with pathnames containing whitespace or control characters (since they will not undergo field expansion). There is also no challenge getting the information back into shell; the shell is doing the processing.

However, if a pathname begins with "-", glob will dutifully expand it, confusing any command later. As noted above, the recommended solution is to always prefix a glob with something that does not begin with dash, such as "./".

Remember that globbing normally skips hidden files (those beginning with "."). Often that is what you want. If you want the hidden files in a directory instead, you may want to use "find" instead. You can get the hidden files with a glob by adding two more globbing patterns:

```shell
 .[!.]* ..?*
```

In many cases even a simple glob could fail to match, and adding globbing patterns to find hidden files makes this even more likely... which leads us to the problem of handling empty pathname lists.

## 4.1 如果可能存在文件名的空列表时，请留意 globs
Beware of globbing if there might be no matches with the pattern (and this is often the case). By default, if a glob like ./*.pdf matches no files, then the original glob pattern will be returned instead.

This is almost never what you want. E.g., in a "for" loop this will cause the loop to execute once, but with the pattern instead of a pathname! Similarly, if you use a glob on a command line, such as cat ./*pdf, the result will be a request to open a non-existent file... which is almost never what you want.

You can use use globbing in a for loop, even if it might not match anything, using one of two approaches. One approach, which is completely portable, is to re-test for the existance of the file before using it in the loop:

```shell
 for file in ./* ; do        # Use this, NOT "for file in *"
   if [ -e "$file" ] ; then  # Make sure it exists and isn't an empty match
     COMMAND ... "$file" ...
   fi
 done
```

This is both ugly and a little inefficient (you have to re-test each file again). There are also pathological cases where the pattern doesn’t match but there is a file that is identical to the unmatched pattern (though for typical patterns that can’t happen), so you have to check your pattern to see if that could happen.

A more efficient but nonstandard solution for empty matches is to use a nonstandard shell extension called "null globbing". Null globbing fixes this by replacing an unmatched pattern with nothing at all. In bash you can enable nullglob with "shopt -s nullglob". In zsh, you can use setopt NULL_GLOB for the same result. Then this will work correctly:

```shell
 shopt -s nullglob  # Bash extension, so that empty glob matches will work
 for file in ./* ; do        # Use this, NOT "for file in *"
     COMMAND ... "$file" ...
 done
```

Null globbing can work well on the command line too, but there’s a catch. If all patterns might be empty, you have to include at least one file (such as /dev/null) that is okay to include, or it needs to be okay to run the command without any pathname arguments. Thus, you can use "cat ./*.pdf /dev/null".

Another problem with globbing is that if the list of matches is too long, on some older shells it will also fail. In short, in robust scripts, globbing should normally be used only as a "for" loop’s list.

## 4.2 globalstar 扩展
Traditional globbing is only useful when you want to process files in a particular directory. Some shells have added a nonstandard "globstar" extension, but it’s both nonstandard and has various limitations. I discuss it here, but you probably want to use find (discussed next).

With the globstar extension, the pattern "**" returns every pathname (including directories) in the current directory, recursively; it omits dot files, doesn’t descend into dot dirs, and sorts the file list.

Bash version 4 recently added this, but you must enable it with "shopt -s globstar". The zsh shell originally came up with this, and ksh93 was the first to copy it (but in ksh you have to enable it with "set -G"). Note that there’s no standard way to invoke it!

If you use this in a for loop list and combine it with nullglob, you can handle absolutely all pathnames easily and efficiently, including the empty case. That sounds great, but watch the fine print... I think there are many reasons to avoid this right now. It’s nonstandard, and gives you little control over the recursion. Most importantly, at least some implementations have trouble if there are links in the directories. Bash 4, at least, can get stuck in infinite loops if there are links. In many cases, find is currently the better approach for reliably doing recursive descent into directories.

# 5 Find
If you want to process files beyond what normal globbing can do (e.g., recursively handle directories), or you don’t like the limitations on having to re-check for non-matches, use find. Re-implementing accurate directory traversal in the shell is possible, but both painful and silly; you would have to deal with symbolic links, hard links, renames during traversal, and other problems. The find tool is designed to handle this job; let it do its job. Sometimes you want to retrieve pathnames from programs other than find; in those cases the issues tend to be very similar.

An advantage of find is that it has lots of options for controlling how you process files and directories. You can use options to limit it to one directory, determine the ordering, and so on. It normally processes all files (including hidden ones), but you can use this pattern to skip hidden files (omit the "?" to skip directory "."):

```shell
 find . -name '.?*' -prune -o ....
```

The find command is always passed a starting directory, and it always returns values beginning with that directory. Thus, as long as the starting directory doesn’t begin with "-" you won’t have a problem with leading "-".

If you can directly use the find "-exec" option to run the command you want to use with the file, that is the easy way. Sadly, this is awkward to do in many cases, so you often want to get pathames from find back into the shell.

A challenge with find (and any other external program) is that it is more complicated to portably get information back into the shell. Pathnames can embed newlines; that means that reading filenames line-by-line fails (e.g, by read), splitting using IFS and newline cannot work directly, and command command substitution $(...) is awkward because it strips away trailing newlines. There are additional problems with Cygwin; Cygwin sometimes silently maps ending \r\n into \n, but it is legal to have filenames that differ only because one ends in \r\n and the other ends in \n; As a result, some constructs for handling arbitrary filenames do not work on Cygwin. Pathnames can also embed tabs and spaces, which by default causes unwanted field splitting. The usual approach is to separate pathnames with \0, but the mechanisms to handle this are not in the POSIX standard (e.g., options for "read" or "xargs" to handle such things).

Some of these approaches use "read", which is tricky to use. You typically need to use the "-r" option (so backslash is considered part of the line, and not an escape mechanism; otherwise filenames containing backslashes will cause problems). You also typically have to set IFS to be empty for the read command; otherwise, a pathname that includes IFS characters at the end of a filename would be corrupted (see the POSIX.1-2008 specification lines 103920-103925). If you want to read pathnames separated by \0 you’ll need another option, the discussion on null-separated pathnames gives more detail.

You’d like to use simple "for" loops, but by default if there is a pathname (glob) expansion character like "*" in a pathname, find will return the "*". If the shell receives a glob expansion character from a command substitution, by default the character will be re-expanded by the shell. You cannot just quote that expansion, either; that would make it appear that the list is just one pathname. Thus, in many cases you need to use "set -f" to disable expansion of filenames.

You could combine xargs with find using a pipe, and use newlines to separate pathnames, but don’t do it. The problem is that xargs interprets many characters in surprising ways, so it’s hard to use xargs correctly when using newlines as separators. The correct portable way to use xargs with newline separators requires that you pipe pathnames through another command like sed to do character substitutions. The result is complicated, hard to read, rediculously inefficient, and isn’t better than many other alternatives (e.g., it doesn’t handle newlines either); here it is:

```shell
 find . | sed -e 's/[^A-Za-z0-9]/\\&/g' | xargs -E "" COMMAND # DO NOT DO
```

# 6 使用以空分割的路径名

If you want to exchange pathnames (in their full generality) between programs, or store them for later, a common solution is to use byte 0 (aka \0 or null) to separate pathnames. This works because pathnames, by definition, cannot include byte 0.

This is very useful, and it works nicely, but note that there are downsides to this approach:

- The easy way to use this convention requires the use of nonstandard extensions that are not a part of the POSIX standard, so the result is non-standard and less portable. Still, many toolsets have a few tool extensions to support this, including the GNU, *BSD, and busybox toolsets. Hopefully someday POSIX will add this.
- The option names to use this convention today (when available) are jarringly inconsistent. In particular, perl uses -0, while the GNU tools options are as follows: sort -z, find -print0, xargs -0, and grep accepts either -Z or --null. The equivalent option in the bash shell read command is -d "" (aka empty delimeter).
- This convention is supported by only a few tools (e.g., the GNU toolset includes support, but in only a few of its tools).
- This format is more difficult to view and modify, in part because so few tools support it, compared to the line-at-a-time format that is widely supported.
- Most shells cannot store byte 0 in a variable at all. You can’t even pass such null-separated lists back to the shell via command substitution; cat $(find . -print0) and similar "for" loops don’t work. Even the POSIX standard’s version of "read" can’t use \0 as the separator (POSIX’s read has the -r option, but not bash’s -d option).
But if you want maximum generality when recursing into subdirectories, this is a common and relatively painless way to do it.

# 7 Encoding pathnames
It is possible to encode pathnames so that all pathnames can be handled. There is no standard POSIX mechanism for doing this encoding, unfortunately.

encodef is a small utility I wrote that can encode and decode filenames in a few formats. With it, you can do this:

```shell
 # This version is POSIX portable; in practice
 # you can often use "-print0" instead of "-exec printf '%s\0' {} \;"
 for encoded_pathname in $(find . -exec printf '%s\0' {} \; | encodef ) ; do
   file="$(encodef -d -Y -- "$encoded_pathname")" ; file="${file%Y}"
   COMMAND "$file" # Use quoted "$file", not $file, everywhere.
 done
```

# 8 快速了解换行

Newline can be a little tricky to get into a shell variable. You can’t do:

```shell
  newline="$(printf '\n')"
```

Because after the $(...) command is executed, any trailing newline is removed.
One alternative is:

```shell
newline='
'
```

But this can get corrupted by programs that change the encoding of file end-of-lines.
The following is a standards-compliant trick to get newline into a variable:

```shell
newline="$(printf '\nX')"
newline="${newline%X}"
```

That is a pain, obviously. More recently, POSIX added support for $'...'. Most shells, though not all, already support it. On a shell that does, you can do this:

```shell
newline=$'\n'
```

# 9 是否可以更改 POSIX 标准以简化文件处理?

The POSIX standard could (and should!) be modified to make it easier to to handle the outrageously permissive pathnames that are permitted today. Basically, we need extensions to make globbing and find easier to use.

## 9.1 Globbing
There are two basic problems with globbing:

1. Globbing in shell returns junk (the pattern) when there are zero matches. There should be a shell option (typically called a "nullglob" option) so an empty list is returned if nothing matches and there was at least one metacharacter. Oddly enough, the underlying glob() function has an option that’s close to this, but there’s no standard way for shells to take advantage of it! Bash, ksh, and others have support, but not in a common standard way, and glob() doesn’t support exactly what is needed either (bugid:247).
2. Globbing normally replies pathnames beginning with "-". There should be an option that when set prepends "./" to any glob result that begins with "-". This should be an option for glob(), as well as for the shell. I think the standard should also state that implementations may enable this by default. Not all real-world commands support "--", and users often forget to add it; we need to have a mechanism to automatically deal with pathnames beginning with "./" if you need them.

## 9.2 Find / null separators
There also needs to be standard way to use find with arbitrary pathnames. The normal way to handle this is by separating pathnames with the null (\0) character; a few changes would simplify this:

1. Extend existing commands to generate or use null-separated pathname lists. At the least, add "find -print0" (bugid:243) and "xargs -0" (bugid:244) since these are already widely implemented. For consistency, I think "-0" should be the standard option name for null-separated lists. It’d be useful to add "grep -0" (GNU grep accepts either -Z or --null) and "sort -0" (GNU sort uses -z). Once added, this common template would be portable:

```shell
 find . -print0 | xargs -0 COMMAND
```

2. Extend the shell’s read so that it can easily read null-separated streams. (bugid:245) Bash can do this today, but it’s painful; the command is IFS="" read -d "" -r which is overly complicated I believe there should be a new "-0" option for read, which says "ignore IFS, and just read until the next \0 byte" (Here’s a bash 4.1 patch). You can then do this (which makes it easier to have long command sequences, as long as you don’t need stdin):

```shell
 find . -print0 | while read -0 file ; do ... done
```

3. Extend the shell so that its for loop can handle a null-separated list. This one is harder; it’s not obvious how to do this. My current theory is that there be a new shell option ‘nullseparator"; when enabled, IFS is ignored, and instead \0 is the input seperator. Then, extend the shell’s for loop syntax so that if you say then in instead of in, this mode is temporarily enabled while the list is processed (the original setting is then restored). (I originally had null in, but using then in means that no new keywords are needed.) You could then do this:

```shell
 for file then in $(find . -print0) do ... done
```

As a side note, it’d be nice if the $'...' construct was standard, as it makes certain things easier (bugid:249).

# 10 如果文件名受到限制，效果会更好么?

在许多情况下，Shell 编程非常简单。 可悲的是，这种常见情况（即文件处理）却远比设想下的 shell 编程情况要更复杂。 这不是仅限于 shell 的问题； 虽然 shell 特别棘手，但同样也很难在所有语言中正确处理 POSIX 路径名。

从根本上说，是因为关于路径名的规则过于宽松。 扩展 POSIX 会使对其处理变得更容易一些，我们也应该这样做。 其实，如果系统对路径名强加一些简单的规则，例如禁止控制字符（bugid：251），禁止前导“-”以及要求路径名是 UTF-8 格式，那处理起来就简单多了。 如此一来，你总是可以安全地打印路径名，这些“正常”的 shell 结构也将始终有效：


```shell
 # 如果路径名从不以“-”开头并且启用了nullglob，则此方法有效:
 for file in *.pdf ; do ... done           # Use "$file" not $file
 # 如果路径名没有控制字符并且 IFS 是制表符和换行符，则此方法有效:
 set -f
 for file in $(find .) ; do ... done        # Use "$file" not $file
```

安全方面的一个很好的一般原则是，您应该将输入列入白名单，并且只接受通过白名单的模式。 但是，目前大多数内核都没有将文件创建列入白名单的机制； 他们只会生成任何想得到的垃圾。 由于他们基本上接受任何东西，因此，导致后期要防范数据变得更加困难。

我认为我们应该扩展 POSIX 标准，并且限制允许的路径名。 并非所有系统都会限制路径名，因此我们需要为它们提供标准机制。 但是新的标准机制不能像限制路径名那样简单； 限制路径名使系统更容易正确使用。

请参阅我关于修复 Unix/Linux 文件名的论文以了解更多信息。

我还做了一些关于如何编码/解码路径名/文件名的工作； 有关更多信息，请参阅 encodef 主页。

但就目前而言，以上就是如何在 shell 程序中正确处理路径名。

## 参考资料

[Filenames and Pathnames in Shell: How to do it Correctly](https://dwheeler.com/essays/filenames-in-shell.html)

## 小白点评
原文是作者 David A. Wheeler 在 2010-05-19 时写的，在 2020-02-22 进行了更新，本译文基于 2020-02-22 的版本进行了翻译。
作者的专业领域应该是安全相关的。