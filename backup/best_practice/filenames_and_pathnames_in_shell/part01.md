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
 # （请记住，globs 匹配通常不包含以".”开头的文件）
 for file in ./* ; do        # 以"./*”为前缀，切勿仅仅以"*”开头
   if [ -e "$file" ] ; then  # 确保它不是空匹配
     COMMAND ... "$file" ...
   fi
 done
```

```shell
 # 正确的便捷的 glob 使用，包括隐藏文件（以".”开头）：
 for file in ./* ./.[!.]* ./..?* ; do        # 以 "./*" 为前缀
   if [ -e "$file" ] ; then  # 保证不是空匹配
     COMMAND ... "$file" ...
   fi
 done
```

```shell
 #正确的 glob 使用，更简单但需要非标准的 bash 扩展 nullglob：
 shopt -s nullglob  # Bash 扩展，因此没有匹配项的 glob 返回空
 for file in ./* ; do        # 使用"./*”，永远不要直接使用"*”
   COMMAND ... "$file" ...
 done
```

```shell
 # 正确的 glob 使用，更简单但需要非标准的 bash 扩展 nullglob；
 # 如果你可以添加 /dev/null 作为输入，你可以在一行上做一些事情。
 shopt -s nullglob  # Bash 扩展，因此没有匹配项的 glob 返回空
 COMMAND ... ./* /dev/null
```

## 1.3 模板：使用 find

find 命令非常适合用于递归处理目录。 通常，你会指定要 find 的其他参数（例如，使用 "-type f” 仅选择普通文件）。 例如，以下是使用 find 遍历文件系统、跳过所有 "隐藏” 目录和文件（名称以".”开头）并仅处理以 `.c` 或 `.h` 结尾的文件的示例：

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
 # find... while 的循环，需要 find (-print0) 和 shell (read -d) 扩展。
 # 会在 Cygwin 上失效; 在 while 循环体中, filenames ending in \r \n and \n look =.
 # 变量值可能会丢失未设置，因为循环可能在子 shell 中运行。
 find . -print0 | while IFS="" read -r -d "" file ; do ...
   COMMAND "$file" # 在任何位置，都使用带引号的 "$file”，而不是 $file。
 done
```

```shell
 # while + find 带有进程替换。
 # 需要非标准 read -d (bash ok) 和 find -print0。
 # 要求非标准进程重定向 <(...); bash、zsh 和 ksh 93
 # 有进程重定向，但 dash 和 ksh 88 没有。 
 # 而且，底层系统必须有命名管道 (FIFO) 或 /dev/fd。
 # 会在 Cygwin 上失效; 在 while 循环体中, filenames ending in \r \n and \n look =.
 # 变量 *do* 在循环结束后保留其值，
 # 并且，你可以从 stdin 读取（如果需要 fd 4，请将 4s 更改为另一个数字）：
 while IFS="" read -r -d "" file <&4 ; do
   COMMAND "$file" # 在任何位置，都使用带引号的 "$file”，而不是 $file。
 done 4< <(find . -print0)
```

```shell
 # 命名管道版本。
 # 需要非标准扩展来 find (-print0) 和 read -d (bash ok)；
 # 底层系统必须 inc. 命名管道 (FIFO)。
 # 会在 Cygwin 上失效; 在 while 循环体中, filenames ending in \r \n and \n look =.
 # 变量 *do* 在循环结束后保留其值，并且
 # 并且，你可以从 stdin 读取（如果需要 fd 4，请将 4s 更改为另一个数字）：
 mkfifo mypipe
 find . -print0 > mypipe &
 while IFS="" read -r -d "" file <&4 ; do
   COMMAND "$file" # 在任何位置，都使用带引号的 "$file”，而不是 $file。
 done 4< mypipe
```

```shell
 # 使用作者的 encodef 程序。
 # 变量 *do* 在循环结束后保留其值。
 # 这个版本是 POSIX 可移植的，但你必须有 encodef。 
 # 在实际使用中，你可以经常使用"-print0”而不是POSIX"-exec printf '%s\0' {} \;”
 for encoded_pathname in $(find . -exec printf '%s\0' {} \; | encodef ) ; do
   file="$(encodef -dY -- "$encoded_pathname")" ; file="${file%Y}"
   COMMAND "$file" # 在任何位置，都使用带引号的 "$file”，而不是 $file。
 done
```

### 1.3.2 局限性

有时不完全处理路径名会更容易，尤其是当你尝试编写可移植的 shell 代码时。但是，如果你使用该代码来检查扩展档案（例如 zip 或 tar 文件），或检查包含由其他人创建的文件的目录（例如，远程文件系统、由其他人或攻击者控制的虚拟机、另一个移动应用程序等），该代码很快就会成为安全漏洞）。以下是示例（及其限制）：

```shell
 # 如果路径名不能包含制表符或换行符，则可以；要留意假设：
 IFS="$(printf '\n\t')"
 set -f # 需要带 * 等的文件名； 有关保存/恢复的信息，请参见下文
 for file in $(find .) ; do
   COMMAND "$file" ...
 done
```

```shell
 # 如果路径名不能包含制表符或换行符，则可以；要留意假设：
 IFS="$(printf '\n\t')"
 set -f # 需要带 * 等的文件名； 有关保存/恢复的信息，请参见下文
 COMMAND $(find .) /dev/null
```

```shell
 # 如果路径名不能包含换行符，则可以；要留意假设：
 # 此外，这会使 stdin 无法访问，并且变量可能不会保持设置。
 find . | while IFS="" read -r file ; do ...
   COMMAND "$file" # 在任何位置，都使用带引号的 "$file”，而不是 $file。
 done
```

```shell
 # 即使你避开了奇葩的文件名，而目录里还是有奇葩的文件名，你也可以安全地使用上述方法
 # 比如说，如下就是如何跳过带有嵌入控制字符的路径名，包括换行符和制表符：
 IFS="$(printf '\n\t')"
 controlchars="$(printf '*[\001-\037\177]*')"
 set -f # 需要带 * 等的文件名； 有关保存/恢复的信息，请参见下文
 for file in $(find . ! -name "$controlchars") ; do
   COMMAND "$file" ...
 done
```

```shell
 # 跳过带有嵌入控制字符的路径名，包括换行符和制表符：
 IFS="$(printf '\n\t')"
 controlchars="$(printf '*[\001-\037\177]*')"
 set -f # 需要带 * 等的文件名； 有关保存/恢复的信息，请参见下文
 COMMAND $(find . ! -name "$controlchars") /dev/null
```

```shell
 # 如果文件名包含控制字符（包括制表符、换行符和 ESC）或 DEL，则这是一种快速退出程序的方法
 # 感谢 Michael Thayer 提出的这个建议。
 expr "$filename" : "`printf '.*[\01-\037\0177*?]'`" && exit 1
```

## 1.4 模板：设置一个变量

没有简单的可移植方法来处理一个变量中的多个任意文件名，然后直接使用它们。Shell 数组可以工作，但在这种情况下使用起来可能很棘手，而且不可移植。我建议禁止带有制表符和换行符的文件名； 那么你可以轻松地将这些字符用作分隔符，如下所示：

```shell
# 如果在字符串中建立选项，请使用制表符|换行符分隔文件名
 IFS="$(printf '\n\t')"
 tab="$(printf '\t')"
 command_options="-x${tab}-y"
# 如果你想把路径名放在内置字符串中，在路径名中防止出现 tab|newline，使用"set -f”，然后你可以使用一个不带引号的变量。
# 例如，假设 $file 不包含 tab|newline，-F $file 是：
 command_options="${options}-F${tab}${file}"
 set -f # 需要带 * 等的文件名； 有关保存/恢复的信息，请参见下文 1.5 章节
 mycommand $command_options "$another_pathname"
```

## 1.5 模板：保存和还原 "set -f"

有时你需要在 shell 中禁用 globbing，尤其是在从 find 接收信息时。POSIX 包括各种便捷的机制，来禁用和重新启用 shell 中的 globbing。"set -f”命令禁用文件通配。 你可以使用"set -f”禁用文件通配，并使用"set +f”重新启用它。 但是，如果你想使用"set -f”暂时禁用文件通配，然后恢复之前的所有内容，该怎么办？ 一种方法是将"set -f”及其所依赖的内容放在子shell中； 这样可行，但是一旦子shell完成，变量设置就会丢失。 你还可以通过执行以下操作来保存和恢复 shell 选项设置：

```shell
 oldSetOptions=$(set +o)             # 保存 shell 选项设置
 ... (set -f, etc.)
 eval "$oldSetOptions" 2> /dev/null  # 恢复 shell 选项设置
```