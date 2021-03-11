# vim 操作基础

## vim 初体验

- 插入模式：i（insert）、a（append）、o（open a line below），或者 I（insert before line）、A（append after line）、O（append a line above）
- normal 模式：插入模式下，按 键盘左上角 Esc
- normal 模式下，保存 :q、保存并退出 :wq、直接退出 :q、不保存强制退出 :q! 其中 w 即 write，q 即 quit

## vim 模式介绍

vim 和其他很多编辑器的区别在于——vim 存在多种模式，并且在编辑过程中，需要频繁地切换

### 默认是普通模式 normal mode
- Q：为什么 vim 进入后，不像其他编辑器一样直接插入？
- A：普通模式下可以进行各种命令行的操作和移动，大部分情况下，是在浏览而不是编辑，所以 vim 的默认模式是 normal

### 编辑时是插入模式 insert mode

- i、a、o 或者 I、A、O
- 使用 Esc 退出 insert mode 到 normal mode

### 命令模式 command mode

- normal 模式下，输入 : 后接执行的命令
  - :wq 保存退出
  - :vs 垂直分屏（vertical split），:sp 水平分屏（split）
  - :% s/foo/bar/g 全局替换 所有的 foo 变为 bar 

### 可视模式 visual mode

- normal 模式下，使用 v 进入 visual 选择， 使用 V（即shift+v）为行选择，使用 ctrl+v 即块选择
- 经常用 visual 模式，进行批量的操作

### 模式切换示意图

- 待补充

## vim 插入模式

### 如何快速纠错

- 进入 vim 之后，首先使用 a/i/o 之一，进入插入模式
- ctrl+h 删除上一个字符，ctrl+w 删除上一个单词，ctrl+u 删除当前行
- 以上几个快捷键，可以用在很多终端中

### 快速切换 insert 和 normal 模式

- 当前为 insert 模式时，使用 ctrl+c 或者 ctrl+\[， 代替 Esc，从 insert 模式切换到 normal 模式
- 当前为 normal 模式时，使用 gi，快速跳转到最后一次编辑的地方，并进入 insert 模式
- 键盘的选择会影响编辑的速度
  - 选择 Esc 和 Ctrl 键比较容易触碰到的键盘
  - 使用 Vim，可以用 HHKB 或 Poker2 这种 mini 键盘
  - 使用软件修正键位

> [!TIP]
> 尽量抛弃之前使用退格键回退的操作，使用 ctrl 快捷键来完成编辑删除工作

## vim 快速移动

### 反人类的 hjkl

> [!NOTE]
> - [Why does vim use hjkl for cursor keys?](https://vi.stackexchange.com/questions/9313/why-does-vim-use-hjkl-for-cursor-keys)
> - 《Unix&Linux大学教程》（Harley Hahn著）第513页和第522页里的解释—— 1976年Vi发明人BillJoy使用的ADM-3A终端的键盘上的Esc键在现在键盘的Tab键的位置，而方向键刚好在HJKL上，所以他这就这么规定了。

### 单词之间移动

- w/W 移动到下一个 word/WORD 的开头
- e/E 移动到下一个 word/WORD 的末尾
- b/B 回退到上一个 word/WORD 的开头，可理解为 backword
- word 指的是以非空白符分割的单词，WORD 是以空白符分割的单词

### 行间搜索移动

- 同一行之间进行快速移动的方式，其实是搜索一个字符，并且移动到该字符
- 使用 f{char} 可以移动到 char 字符上，t 移动到 char 的前一个字符
- 如果第一次没有搜到，可以使用分号（；）/逗号（，），继续搜索该行的下一个/上一个
- 大写的 F 表示反过来，搜前面的字符

### vim 的水平移动

- 使用 0 移动到行首的第一个字符，使用 ^ 移动到第一个非空白字符
- 使用 $ 移动到行尾，g_ 移动到行尾非空白字符

> [!TIP]
> - 只记住常用的 0 和 $ 即可以满足绝大部分日常需求了
> - ^ 等价于 0w

### vim 的垂直移动

- 使用括号() 在句子之间移动，可以使用 :help( 来查看其帮助
- 使用 {} 在段落之间进行移动

> [!TIP]
> 使用 easy-motion 插件后，就不用记这些个命令了

### vim 的页面移动

- gg 移动到文件的开头，G 移动到文件的结尾
- 可以使用 ctrl+o 进行快速返回
- H/M/L 跳转到屏幕的开头（Head）、中间（Middle）和底部（Lower）
- ctrl+u ctrl+f 上下翻页（upword/forward），zz 把屏幕置为中间

### 本节小结

- 这些移动命令都是在 normal 模式下的命令，因为我们很大一部分时间，是用在跳转移动上面。
- 在 normal 模式下，使用 :help xxx , 查询 xxx 相关的帮助文档，使用 :q 返回 normal 模式

## vim 快速增删改查

### vim 增加字符

- 使用a/i/o 进入插入模式，或者 A/I/O 进入

### vim 快速删除

- 在 normal 模式下，使用 x 快速删除一个字符
- 在 normal 模式下，使用 d（delete）配合文本对象，快速删除一个单词 dw， 也有 daw（delete around word），diw（delete inner word），使用 dw 默认执行 daw
- 在 normal 模式下，使用 dd 快速删除一行
- d 和 x 可以搭配数字多次执行，如 3d，5x 等
- 在 normal 模式下，使用 d$ 快速删除到行尾
- 在 normal 模式下，使用 d0 快速删除到行首

> [!TIP]
> - 光标置于（）内的左括号后一位时，想要快速删除括号内的内容，使用 dt) ，即 delete to )
> - 同理，光标置于 "" 内的左引号后一位时，想要快速删除括号内的内容，使用 dt"

- 在 visual 模式下，即 normal 模式下按 v 进入，或者按 V 进入行选择的 visual 模式

> [!NOTE]
> 总的删除操作，还是围绕着 x 和 d 进行的

### vim 快速修改

- 常用的有三个命令—— r，c，s ，分别代表 replace、change、substitute
- normal 模式下，使用 r 替换一个字符，使用 R 一直替换
- normal 模式下，使用 s 替换并进入插入模式， 使用 S 删除整行至行首，并进入插入模式
- normal 模式下，使用 c 配合文本对象，可以进行快速的修改， 使用 C 删除当前光标后整行，并进入插入模式
- 同样可以搭配数字使用

> [!TIP]
> - 使用 cw 删除单词并进入插入模式 (change word)
> - 和删除操作类似，光标置于 "" 内的左引号后一位时，使用 ct"，快速删除括号内的内容，并进入插入模式

### vim 查询

- normal 模式下，使用 / 或者 ? 进行前向或者后向的搜索
- normal 模式下，使用 n/N 跳转到下一个或者上一个匹配
- normal 模式下，使用 * 或者 # 进行当前单词的前向和后向匹配

## vim 的搜索替换

### vim 的替换命令

- substitute 命令允许我们查找并替换掉文本，并且支持正则表达式
- 格式：`：[range]s[ubstitute]/{pattern}/{string}/[flags]` 
  - range：表示范围，比如：10,20 表示 10-20 行，% 表示全部
  - pattern： 表示要替换的模式
  - string： 表示替换后的文本
  - flags：表示替换标志，其中，g（global）表示全局范围内执行，c（confirm）表示确认，可以同意或者拒绝修改，n（number）报告匹配到的次数而不替换，用来查询匹配次数
  - /: 表示分隔符，可以用其他字符如 #, @ 替换，只要前后统一即可

### 正则的使用
> [!TIP]
> 替换的时候，可以使用正则进行匹配，如精确匹配全文范围内，某替换模式的内容，可以使用 :% s/\<pattern\>/string/g

### 插件的辅助

- Q：如何批量替换多个文件中的匹配呢？
- A：使用插件来批量搜索替换多个文件中的匹配

## vim 多文件操作

### 多文件操作的相关概念

- Buffer：是指打开的额一个文件的内存缓冲区
- Window：窗口，是指 buffer 可视化的分割区域
- Tab：可以组织窗口为一个工作区

### Buffer 缓冲区

- vim 打开一个文件后，会加载文件内容到缓冲区，之后的修改都是针对内存中的缓冲区，并不会直接保存到文件，直到我们执行 :w(write) 的时候，才会把修改内容写入到文件里

#### Buffer 切换

- 使用 :ls 会列举当前的缓冲区，然后使用 :b n 跳转到第 n 个缓冲区
- 使用 :bpre  :bnext  :bfirst  :blast 跳转到 之前，之后，第一，最后一个缓冲区
- 或者使用 :b buffer_name  加上 tab 补全来跳转

### Window 窗口

- 窗口是可视化的分割区域
- 一个缓冲区，可以分割成多个窗口，每个窗口也可以打开不同缓冲区
- <ctrl+w>s 水平分割，<crtl+w>v 垂直分割。或者命令模式下 :sp 和 :vs 
- 每个窗口可以继续被无限分割

#### 切换窗口

#### 重排窗口

### Tab 标签页，将窗口分组

- Tab 是可以容纳一系列窗口的容器（:h tabpage）
- vim 的 Tab 和其他的编辑器不太一样，可以想象成 Linux 的虚拟桌面，比如一个 Tab 全用来编辑 Python 文件，一个 Tab 全是 HTML 文件。
- 相比窗口，Tab 一般用的比较少，Tab 太多管理起来也比较麻烦。

> [!NOTE]
> - 窗口分割可以非常方便地查看代码，比如查看整个调用链。
> - 配合 ctrlp 插件和 nerdtree 可以快速操作多个文件。

## vim 的 text object

### Text Object 文本对象

- 主流的编程语言，有面向对象编程的概念。vim 中，同样有文本对象的概念，比如一个单词、一段句子、一个段落。
- 很多其他编辑器经常只能操作单个字符来修改文本，比较低效。

### 文本对象的操作方式

- 格式：`[number]<commmand>[text object]` 即 `次数 + 命令 + 文本对象` 
  - number：表示次数
  - command：表示命令，有 删除 d（delete），修改 c（change），复制 y（yank），可视选择v（visual）
  - text object：表示要操作的文本对象，比如单词 w（word），句子 s（sentence），段落 p（paragraph）

> [!TIP]
> - 记住最常用的命令模式：d、c、y、v，以及文本对象 w 即可摆脱低效的字符操作，使用文本对象提高编辑效率。
> - 有些插件扩展了文本对象的概念，比如说 vim-go 可以使用 f（function）表示一个函数。

## vim 的复制粘贴与寄存器使用

### 在 normal 模式下复制粘贴

- normal 模式下复制粘贴分别使用 y（yank）和 p（put）， 剪切 d 和 p
- 可以使用 v（visual）命令选中所要复制的地方，使用 y 进行复制，然后使用 p 进行粘贴
- 也可以配合文本对象：比如使用 yiw 复制一个单词，yy 复制一行

> [!ATTENTION]
> 常规定义 vs vim的定义
> - 剪切： cut | delete
> - 复制： copy | yank （因为已经被 c（change）占用了）
> - 粘贴： paste | put

### 在 insert 模式下复制粘贴

- 很多人会使用鼠标进行选中，然后使用 ctrl+v 或者 cmd+v 进行粘贴

> [!ATTENTION]
> **vim 在粘贴代码的时候有个大坑！**，因为很多人在 vimrc 中设置了 autoindent，这样粘贴代码是缩进会错乱，这个时候，需要在粘贴之前及之后，使用 :set paste 和 :set nopaste 进行解决。
> 其实，也是有插件来更便捷地解决这个问题的。

### vim 寄存器

- Q: vim 在 normal 模式下，复制/剪切的内容都去哪里了呢？
- A：vim 里面操作的是寄存器而不是系统剪贴板，这是和其他编辑器不同的地方。
- 默认使用 d 删除或者 y 复制的内容，都放在了 “无名寄存器”中。
- 用 x 删除一个字符放到无名寄存器，然后用 p 粘贴，可以调换两个字符

#### 深入寄存器（register）

- vim 不适用单一剪贴板进行剪贴、复制与粘贴，而是多组寄存器
- 通过 `"{register}` 前缀可以指定寄存器，不指定则默认使用无名寄存器。
  - 比如 使用 "ayiw 则是复制一个单词到寄存器 a 中
  - 使用 "bdd 则是删除当前行到寄存器 b 中
- 使用 :reg [register_name] 则查看相应寄存器中的内容
- vim 中 "" 表示无名寄存器，缺省使用。

#### 其他常见寄存器

- 除了有名寄存器 a-z 外，vim 还有一些其他常见寄存器：
  - 复制专用寄存器："0 使用 y 复制文本，同时会被拷到复制寄存器 0
  - **系统剪贴板： "+ 可以在复制之前，加上 "+ 复制到系统剪贴板**
  - 其他一些寄存器，比如 "% 当前文件名， ". 上次插入的文本

> [!TIP]
> - **normal 模式下，执行 :set clipboard=unnamed 之后，可以直接复制粘贴系统剪贴板的内容，这样就不用调寄存器了**
> - 上一节中，代码粘贴的操作，这里也可以用系统剪贴板功能来代替，如果已经复制，并且发现复制后缩进错乱，可以如下操作：
>   - :e! 重新加载一下
>   - "+p 在normal 模式下进行粘贴
>   - 或者，在 vimrc 中设置了 :set clipboard=unnamed 之后，直接使用 p 就可以用系统剪贴板的内容来粘贴了。
> - 在服务器的 vim 上，粘贴下系统剪贴板的内容，只能用 :set paste :set unpaste 这种方式

> [!WARNING]
> 这里的 "+p 方式，我目前配置的 vim 没有实现，但是 :set paste 和 :set nopaste 实现了

## vim 使用宏完成批量操作

### 宏（macro）的概念

- 宏 可以看成时一系列命令的集合
- 我们可以使用 宏 [录制] 一系列操作，然后用于 [回放]
- 宏 可以非常方便地把一系列命令用在多行文本上

### 宏的使用

- vim 使用 q 来开启录制，同时，使用 q 来结束录制
- 使用 q{register} 选择要保存的寄存器，把录制的命令保存在其中
- 使用 @{register} 回放寄存器中，保存的一系列命令

### 宏的具体使用——多行文本批量加引号

- Q：从需求来讲起，如何给多行 url 链接加上双引号？
- A1：使用宏来解决批量操作
  - 先给一行录制加上双引号的操作，然后保存到指定寄存器中，退出录制
  - 然后回放到其他的所有行
- A2：**使用 normal 模式下的多个命令进行批量操作**
  - normal 模式下，先 V 进行 行选择，G 选择到行尾
  - 然后 :normal I" , 并回车，可以在选择区域的行首，添加 "
  - 接着，重新 : , 并 使用 ctrl+p ， 可以跳转到上一个命令，再修改下该命令为 :normal A"，按回车，即可在行尾添加命令

> [!NOTE]
> - 什么时候，会用到宏呢？

## vim 补全

### 什么是补全

- 补全，即是编辑器根据当前的环境上下文，去猜测你想输入的东西。比如，补全一个单词、文件名，或者代码中的函数名、变量名等
- vim 提供了多种补全功能，还可以由插件拓展功能实现代码补全

### 常见的三种补全类型

- **使用 ctrl+n 和 ctrl+p 补全单词**
- **使用 ctrl+x 和 ctrl+f 补全文件名**
- **使用 ctrl+x 和 xtrl+o 补全代码，需要开启文件类型检查，安装插件**

> [!TIP]
> Q：vim 编辑时，如何快速在文件中输入当前文件名？
> A：normal 模式下，执行 `:r! echo %`，然后 enter，如果要输入全路径，`:r! echo %:p` 
> - 可以通过 :help read 和 :help filename-modifiers 查看

## vim 配色

### vim 更换配色

- 使用 :colorscheme 显示当前的主题配色，默认是 default
- 使用 :colorscheme ctrl+d 可以显示所有的配色， 有喜欢的配色后，使用 :colorscheme 配色名 即可修改配色
- 想要持久化配置时，需要写到 vimrc 当中

### 配色资源下载

- 如： https://github.com/flazz/vim-colorschemes
- 克隆好仓库后，将相应的 .vim 文件拷贝到 ~/.vim/colors

## vim 小结

- 建议初学者，从裸 vim 开始学习，有些场景，比如服务器，或者容器中，无法配置个性化 vim
- 学会打字指法和盲打，形成肌肉记忆，效率会很高
- 可以在 IDE 工具上安装 vim 插件，几乎所有流行的编辑器和 IDE 都支持 vim 插件。