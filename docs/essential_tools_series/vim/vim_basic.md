# vim 操作基础

## vim 初体验
## vim 模式介绍

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
- 在 normal 模式下，使用 d（delete）配合文本对象，快速删除一个单词 dw， 也有 daw（d around word），diw，使用 dw 默认执行 daw
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

## vim 替换
## vim 多文件操作
## vim 的 text object
## vim 的复制粘贴与寄存器使用
## vim 使用宏完成批量操作
## vim 补全
## vim 配色
## vim 小结