# UNIX 高手的另外 10 个习惯

让我们面对现实吧：坏习惯很难改变。但是您已经熟悉的习惯可能更难克服。有时，重新审视某些事情可能让您遇到“啊哈，我没想到它能做到这一点！”的时刻。在 Michael Stutz 的优秀文章“UNIX 高手的 10 个习惯”的基础上，本文将提供另外 10 个 UNIX 命令行命令、工具和技术，可以使您成为更高效的 UNIX 命令行高手。

您应当采纳的其他 10 个好习惯包括：

>1. 使用文件名自动完成功能 (file name completion)。
2. 使用历史扩展。
3. 重用以前的参数。
4. 使用 pushd 和 popd 管理目录导航。
5. 查找大型文件。
6. 不使用编辑器创建临时文件。
7. 使用 curl 命令行实用工具。
8. 最有效地利用正则表达式。
9. 确定当前用户。
10. 使用 awk 处理数据。
	
附：常用首字母缩写词
```shell
MB：兆字节
HTTP：超文本传输协议
HTTPS：HTTP over Secure Sockets Layer
FTP：文件传输协议
FTPS：FTP over Secure Sockets Layer
LDAP：轻型目录访问协议
```

## 1.使用文件名完成

如果不需要在命令提示符处键入长的、令人费解的文件名，这是不是很棒呢？的确，您不需要这样做。相反，您可以配置最流行的 UNIX Shell 以使用文件名完成。该功能在各个 Shell 中的工作方式略有不同，因此我将向您展示如何在最流行的 Shell 中使用文件名完成。文件名完成使您可以更快地输入并避免错误。懒惰？也许吧。效率更高？当然！

### 我正在运行哪种 Shell？

如果您不知道目前使用的是哪一种 Shell，会怎么样？虽然这个诀窍不是另外 10 个好习惯的正式组成部分，但它仍然很有用。如清单 1 所示，您可以使用 echo \$0 或 ps -p \$\$ 命令显示您正在使用的 Shell。对于我来说，运行的是 Bash Shell。

```shell
#清单 1. 确定您的 Shell              
$ echo $0
-bash
$ ps –p $$
PID TTY           TIME CMD
6344 ttys000    0:00.02 –bash
```

#### C Shell

C Shell 支持最直接文件名完成功能。设置 filec 变量可启用该功能。（您可以使用命令 set filec。）在您开始键入文件名后，可以按 Esc 键，Shell 将完成文件名，或完成尽可能多的部分。例如，假设您拥有名为 file1、file2 和 file3 的文件。如果您键入 f，然后按 Esc 键，将填充 file，而您必须键入 1、2 或 3 来完成相应的文件名。

#### Bash

Bash Shell 也提供了文件名完成，但使用 Tab 键代替 Esc 键。您在 Bash Shell 中不需要设置任何选项即可启用文件名完成，该选项是缺省设置的。Bash 还实现了其他功能。键入文件名的一部分后，按 Tab 键，如果有多个文件满足您的请求，并且您需要添加文本以选择其中一个文件，那么您可以多按 Tab 键两次，以显示与您目前键入的内容相匹配的文件的列表。使用之前名为 file1、file2 和 file3 的文件示例，首先键入 f。当您按一次 Tab 键时，Bash 完成 file；再按一次 Tab 键时，将展开列表 file1 file2 file3。

#### Korn Shell

对于 Korn Shell 用户，文件名完成取决于 EDITOR 变量的值。如果 EDITOR 设置为 vi，那么您键入部分名称，然后按 Esc 键，后跟反斜杠 (/) 字符。如果 EDITOR 设置为 emacs，那么您键入部分名称，然后按两次 Esc 键以完成文件名。

## 2.使用历史扩展

如果您为一系列命令使用相同的文件名，会发生什么情况？当然，有一种快捷方式可以快速获得您上次使用的文件名。如清单 2 所示，!$ 命令返回前一个命令使用的文件名。从文件 this-is-a-long-lunch-menu-file.txt 中搜索单词 pickles 的出现位置。搜索结束后，使用 vi 命令来编辑 this-is-a-long-lunch-menu-file.txt 文件，而不需要重新键入文件名。您使用感叹号 (!) 来访问历史，然后使用美元符号 ($) 返回前一命令的最后字段。如果您反复用到长文件名，那么这是一个非常好的工具。

```shell
#清单 2. 使用 !$ 获得前一个命令使用的文件名               
$ grep pickles this-is-a-long-lunch-menu-file.txt
pastrami on rye with pickles and onions
$ vi !$		    
```

## 3.重用以前的参数

!\$ 命令返回某个命令使用的上一个文件名参数。但如果某个命令使用多个文件名，而您只希望重用其中一个文件名，该如何做？!:1 操作符返回某个命令使用的第一个文件名。清单 3 中的示例显示可以如何将此操作符与 !\$ 运算符组合使用。在第一个命令中，将一个文件重新命名为更有意义的名称，但为了保持原始文件名可用，创建了一个符号链接。重新命名文件 kxp12.c 以提高可读性，然后使用 link 命令来创建到原始文件名的符号链接，以防在其他位置使用该文件名。!\$ 操作符返回 file_system_access.c 文件名，而 !:1 操作符返回 kxp12.c 文件名，该文件名是上个命令的第一个文件名。

```shell
#清单 3. 组合使用 !$ 和 !:1             
$ mv kxp12.c file_system_access.c
$ ln –s !$ !:1
```

## 4.使用 pushd 和 popd 管理目录导航

UNIX 支持各种目录导航工具。我最喜欢的两款提高工作效率的工具是 pushd 和 popd。您当然了解 cd 命令用于更改您的当前目录。如果您要在多个目录中导航，但希望能够快速返回某个位置，该如何做？pushd 和 popd 命令创建一个虚拟目录堆栈，pushd 命令用来更改您的当前目录并将其存储在堆栈中，而 popd 命令用来从堆栈的顶部移除目录并使您返回该位置。您可以使用 dirs 命令来显示当前目录堆栈，而不会压入或弹出新目录。清单 4 显示如何使用 pushd 和 popd 命令在目录树中快速导航。

```shell
#清单 4. 使用 pushd 和 popd 在目录树中导航              
$ pushd .
~ ~
$ pushd /etc
/etc ~ ~
$ pushd /var
/var /etc ~ ~
$ pushd /usr/local/bin
/usr/local/bin /var /etc ~ ~
$ dirs
/usr/local/bin /var /etc ~ ~
$ popd
/var /etc ~ ~
$ popd
/etc ~ ~
$ popd
~ ~
$ popd
```

pushd 和 popd 命令还支持使用参数处理目录堆栈。使用 +n 或 -n 参数，其中 n 是一个数字，您可以向左或向右移动堆栈，如清单 5 所示。

```shell
#清单 5. 旋转目录堆栈               
$ dirs
/usr/local/bin /var /etc ~ ~
$ pushd +1
/var /etc ~ ~ /usr/local/bin
$ pushd -1
~ /usr/local/bin /var /etc ~
```

## 5.查找大型文件

是否需要找出您的所有空闲磁盘空间被什么占用了？您可以使用以下几个工具来管理您的存储设备。如清单 6 所示，df 命令为您显示每个可用卷上已使用的块的总数，以及空闲空间的百分比。

```shell
#清单 6. 确定卷的使用情况               
$ df
Filesystem                            512-blocks      Used  Available Capacity  Mounted on
/dev/disk0s2                           311909984 267275264   44122720    86%    /
devfs                                        224       224          0   100%    /dev
fdesc                                          2         2          0   100%    /dev
map -hosts                                     0         0          0   100%    /net
map auto_home                                  0         0          0   100%    /home
```

是否希望查找大型文件？使用 find 命令时附带 -size 参数。清单 7 显示了如何使用 find 命令来查找大于 10MB 的文件。请注意，-size 参数以 KB 为单位计量大小。

```shell
#清单 7. 查找大于 10MB 的所有文件              		  
$ find / -size +10000k –xdev –exec ls –lh {}/;
```

## 6.不使用编辑器创建临时文件

以下是一个简单示例：您需要快速创建一个简单临时文件，但不希望启动您的编辑器。使用带有 > 文件重定向操作符的 cat 命令。如清单 8 所示，使用不带文件名的 cat 命令只回显向标准输入键入的任何内容；> 重定向将该输入捕获到指定的文件中。请注意，您在结束键入时必须提供文件结束字符，通常为 Ctrl-D。

```shell
#清单 8. 快速创建临时文件               	
$ cat > my_temp_file.txt
This is my temp file text
^D
$ cat my_temp_file.txt
This is my temp file text
```

需要执行相同操作，但是附加到现有文件而不是创建新文件。如清单 9 所示，改用 >> 操作符。>> 文件重定向操作符向现有文件附加内容。

```shell
#清单 9.快速向文件附加内容               
$ cat >> my_temp_file.txt
More text
^D
$ cat my_temp_file.txt
This is my temp file text
More text
```

## 7.使用 curl 命令行实用工具

我是否可以从命令行访问 Web？你疯了吗？没有，这就是 curl 的用途！curl 命令使您可以使用 HTTP、HTTPS、FTP、FTPS、Gopher、DICT、TELNET、LDAP 或 FILE 协议从服务器检索数据。如清单 10 所示，我可以使用 curl 命令从美国国家气象局了解我所在位置（纽约州布法罗市）的当前天气状况。当与 grep 命令组合使用时，我可以检索布法罗市的天气状况。使用 -s 命令行选项来禁止 curl 处理输出。

```shell
#清单 10. 使用 curl 检索当前天气状况               
$ curl –s http://www.srh.noaa.gov/data/ALY/RWRALY | grep BUFFALO
BUFFALO        MOSUNNY   43  22  43 NE13      30.10R
```

如清单 11 所示，您也可以使用 curl 命令来下载 HTTP 托管的文件。使用 -o 参数来指定保存输出的位置。

```shell
#清单 11. 使用 curl 下载 HTTP 承载的文件               
$ curl -o archive.tar http://www.somesite.com/archive.tar
```

这实际上只是您使用 curl 命令可以完成的操作的提示。您只需在命令提示符处键入 man curl 显示 curl 命令的完整使用信息，就可以开始了解更多内容。

## 8.最有效地利用正则表达式

大量 UNIX 命令使用正则表达式作为参数。从技术角度而言，正则表达式 是表示某种模式的字符串（也就是说，由字母、数字和符号组成的字符序列），用于定义零或更长的字符串。正则表达式使用元字符（例如，星号 [*] 和问号 [?]）来匹配其他字符串的部分或全部内容。正则表达式不一定包含通配符，但通配符可以使正则表达式在搜索模式和处理文件时发挥更大的作用。表 1 显示了一些基本正则表达式序列。

表 1. 正则表达式序列

序列 | 说明
:-:|:--:|
脱字符 (^) | 匹配出现在行首的表达式，例如 ^A
美元符号 ($) | 匹配出现在行末的表达式，例如 A$
反斜杠 (\) | 取消下一个字符的特殊含义，例如 \^
方括号 ([])	| 匹配括起来的任一字符，例如 [aeiou]（使用连字符 [-] 表示范围，例如 [0-9]）。
[^ ] | 匹配除括起来字符以外的任一字符，例如 [^0-9]
句点 (.) |	匹配除行尾之外的任意单个字符
星号 (*)	| 匹配零个或多个前驱字符或表达式
\{x,y\} | 匹配出现过 x 到 y 个和前面相同的内容
\{x\}	| 精确匹配出现过 x 个和前面相同的内容
\{x,\} | 匹配出现过 x 个或更多和前面相同的内容

清单 12 显示了与 grep 命令一起使用的一些基本正则表达式。

```shell
#清单 12. 使用正则表达式和 grep               
$ # Lists your mail
$ grep '^From: ' /usr/mail/$USER   
$ # Any line with at least one letter  
$ grep '[a-zA-Z]'  search-file.txt
$ # Anything not a letter or number
$ grep '[^a-zA-Z0-9] search-file.txt
$ # Find phone numbers in the form 999-9999 
$ grep '[0-9]/{3/}-[0-9]/{4/}' search-file.txt
$ # Find lines with exactly one character
$ grep '^.$' search-file.txt
$ #  Find any line that starts with a period "."          
$ grep '^/.' search-file.txt 
$ # Find lines that  start with a "." and 2 lowercase letters
$ grep '^/.[a-z][a-z]' search-file.txt
```

有大量书籍专门讲述正则表达式。有关命令行正则表达式的深入描述，建议您阅读 developerWorks 文章“对话 UNIX，第 9 部分：正则表达式。”

## 9.确定当前用户

有时，您可能希望确定某个特定用户是否运行过您的管理脚本。为找出答案，您可以使用 whoami 命令来返回当前用户的名称。清单 13 显示了独自运行的 whoami 命令；清单 14 显示了使用 whoami 确保当前用户不是根用户的 Bash 脚本的摘录。

```shell
#清单 13. 从命令行使用 whoami               
$ whoami
John
```
```shell
#清单 14. 在脚本中使用 whoami
                
if [ $(whoami) = "root" ]
then
   echo "You cannot run this script as root."
   exit 1
fi
```

## 10.使用 awk 处理数据

awk 命令似乎始终处在 Perl 的阴影下，但它对于简单、基于命令行的数据处理来说是一个快速、实用的工具。清单 15 显示了如何开始使用 awk 命令。若要获取文件中每行文本的长度，请使用 length() 函数。若要查看字符串 ing 是否出现在文件文本中，请使用 index() 函数，该函数返回 ing 首次出现的位置，这样您就可以使用它来进行进一步的字符串处理。若要 tokenize（也就是说，将一行拆分为单词长度的片段）某个字符串，请使用 split() 函数。

```shell
#清单 15. 基本 awk 处理               
$ cat text
testing the awk command
$ awk '{ i = length($0); print i }' text
23
$ awk '{ i = index($0,”ing”); print i}' text
5
$ awk 'BEGIN { i = 1 } { n = split($0,a," "); while (i <= n) {print a[i]; i++;} }' text
testing 
the
awk
command
```

打印文本文件中的指定字段是一项简单的 awk 任务。在清单 16 中，sales 文件包含每个销售人员的姓名，后跟每月销售数字。您可以使用 awk 命令来快速获得每个月的销售总额。缺省情况下，awk 将每个以逗号分隔的值视为不同的字段。您使用 $n 操作符来访问每个字段。

```shell
#清单 16. 使用 awk 对数据进行汇总               
$cat sales
Gene,12,23,7
Dawn,10,25,15
Renee,15,13,18
David,8,21,17
$ awk -F, '{print $1,$2+$3+$4}' sales
Gene 42
Dawn 50
Renee 46
David 46
```

awk 命令可以很复杂并应用于广泛的情景中。若要更完整地学习 awk 命令，请从命令 man awk 开始，并参阅参考资料部分提供的资源。

## 结束语

成为命令行高手需要进行一些实践。按照相同的方式处理问题很简单，因为您已经习惯了。扩展您的命令行资源可以显著提高您的工作效率，并促使您朝着 UNIX 命令行高手的方向前进！


## 参考资料
[Learn 10 more good UNIX usage habits](https://www.ibm.com/developerworks/aix/library/au-unixtips/?S_CMP=cn-a-aix&S_TACT=105AGX52)