# set 命令详解

linux  set 命令

功能说明：设置shell。

语　　法：set [+-abCdefhHklmnpPtuvx]

补充说明：用set 命令可以设置各种shell选项或者列 出shell变量.单个选项设置常用的特性.在某些选项之后-o参数将特殊特性打开.在某些选项之后使用+o参数将关闭某些特性,不带任何参数的set命 令将显示shell的全部变量.除非遇到非法的选项,否则set总是返回ture.

参　　数：

allexport                -a                        从设置开始标记所有新的和修改过的用于输出的变量         

braceexpand         -B                      允许符号扩展,默认选项  

emacs                                            在进行命令编辑的时候,使用内建的emacs编辑器, 默认选项

errexit                   -e                        如果一个命令返回一个非0退出状态值(失败),就退出.

histexpand           -H                      在做临时替换的时候允许使用!和!! 默认选项

history                                           允许命令行历史,默认选项

ignoreeof                                     禁止coontrol-D的方式退出shell，必须输入exit。

interactive-comments                  在交互式模式下， #用来表示注解

keyword             -k                     为命令把关键字参数放在环境中

monitor               -m                      允许作业控制

noclobber           -C                      保护文件在使用重新动向的时候不被覆盖

noexec                 -n                       在脚本状态下读取命令但是不执行，主要为了检查语法结构。

noglob                -d                       禁止路径名扩展，即关闭通配符     

notify                 -b                        在后台作业以后通知客户

nounset              -u                         在扩展一个没有的设置的变量的时候，    显示错误的信息     

onecmd               -t                          在读取并执行一个新的命令后退出       

physical              -P                       如果被设置，则在使用pwd和cd命令时不使用符号连接的路径 而是物理路径

posix                                             改变shell行为以便符合POSIX要求

privileged                                       一旦被设置，shell不再读取.profile文件和env文件 shell函数也不继承任何环境

verbose             -v                            为调试打开verbose模式

vi                                                  在命令行编辑的时候使用内置的vi编辑器

xtrace                  -x                            打开调试回响模式

 

设置系统环境变量在本站下面这两篇文章中有详细说明:
设置环境变量永久生效 export PS1 :http://www.linuxso.com/a/linuxxitongguanli/1812.html

扩展阅读:set,env和export这三个命令都可以用来显示shell变量,其区别?

set 用来显示本地变量
env 用来显示环境变量
export 用来显示和设置环境变量

set 显示当前shell的变量，包括当前用户的变量
env 显示当前用户的变量
export 显示当前导出成用户变量的shell变量

每个shell有自己特有的变量（set）显示的变量，这个和用户变量是不同的，当前用户变量和你用什么shell无关，不管你用什么shell都在，比如HOME,SHELL等这些变量，但shell自己的变量不同shell是不同的，比如BASH_ARGC， BASH等，这些变量只有set才会显示，是bash特有的，export不加参数的时候，显示哪些变量被导出成了用户变量，因为一个shell自己的变量可以通过export “导出”变成一个用户变量

[root@linux ~]# aaa=bbb

[root@linux ~]# echo $aaa
bbb

[root@linux ~]# set|grep aaa
aaa=bbb

[root@linux ~]# env|grep aaa

[root@linux ~]# export aaa

[root@linux ~]# env|grep aaa
aaa=bbb

扩展资料二:set、env、export——Linux中的环境变量命令

Linux是一个多用户的操作系统。每个用户登录系统后，都会有一个专用的运行环境。通常每个用户默认的环境都是相同的，这个默认环境实际上就是一组环境 变量的定义。用户可以对自己的运行环境进行定制，其方法就是修改相应的系统环境变量。

什么是环境变量

环境变量是一个具有 特定名字的对象，它包含了一个或者多个应用程序所将使用到的信息。许多用户（特别是那些刚接触Linux的新手）发现这些变量有些怪异或者难以控制。其 实，这是个误会：通过使用环境变量，你可以很容易的修改一个牵涉到一个或多个应用程序的配置信息。

常见的环境变量

对于 PATH和HOME等环境变量大家都不陌生。

PATH能够指定命令的搜索路径，那么动态链接库的路径用什么变量指定呢？或者就是在 PATH里面？
比如有一个程序需要/usr/local/lib下面的一个库文件，应该怎么指定其路径呢？
经常看到有些变量如 LD_LIBRARY_PATH,LIBPATH,CLASSPATH等，他们之间有什么不同和关系？

除此之外，还有下面一些常见环境变 量。

◆ HISTSIZE是指保存历史命令记录的条数。

◆ LOGNAME是指当前用户的登录名。

◆ HOSTNAME是指主机的名称，许多应用程序如果要用到主机名的话，通常是从这个环境变量中来取得的。

◆ SHELL是指当前用户用的是哪种Shell。

◆ LANG/LANGUGE是和语言相关的环境变量，使用多种语言的用户可以修改此环境变量。

◆ MAIL是指当前用户的邮件存放目录。

◆ PS1是基本提示符，对于root用户是#，对于普通用户是$。PS2是附属提示符，默认是“>”。可以通过修改此环境变量来修改当前的命令符，比 如下列命令会将提示符修改成字符串“Hello,My NewPrompt  ”。
