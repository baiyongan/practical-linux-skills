## shell十三问之4：""(双引号)与''(单引号)差在哪？
------------------------------------------------

还是回到我们的`command line`来吧...

经过前面两章的学习，应该很清楚当你在`shell prompt`后面敲打键盘,
直到按下`Enter`键的时候，你输入的文字就是`command line`了，
然后`shell`才会以进程的方式执行你所交给它的命令。
但是，你又可知道：你在`command line`中输入的每一个文字，
对`shell`来说，是有类别之分的呢？

简单而言，(我不敢说精确的定义，注1),
`command line`的每一个`charactor`, 分为如下两种：

- literal：也就是普通的纯文字，对`shell`来说没特殊功能；
- meta: 对`shell`来说，具有特定功能的特殊保留元字符。

> **Note:**

> 对于`bash shell`在处理`comamnd line`的顺序说明，
> 请参考O'Reilly出版社的**Learning the Bash Shell，2nd Edition**，
> 第177-180页的说明，尤其是178页的流程图：Figure 7-1 ...


`literal`没什么好谈的，
像abcd、123456这些"文字"都是literal...(so easy? ^_^)
但meta却常使我们困惑...(confused?)
事实上，前两章，我们在`command line`中已碰到两个
似乎每次都会碰到的meta：

- `IFS`：有`space`或者`tab`或者`Enter`三者之一组成(我们常用space)
- `CR`: 由`Enter`产生；

`IFS`是用来拆解`command line`中每一个词(word)用的，
因为`shell command line`是按词来处理的。
而`CR`则是用来结束`command line`用的，这也是为何我们敲`Enter`键，
命令就会跑的原因。

除了常用的`IFS`与`CR`, 常用的meta还有：

|meta字符| meta字符作用|
|--------|-------------|
|= |设定变量|
|$ | 作变量或运算替换(请不要与`shell prompt`混淆)|命令
|>| 输出重定向(重定向stdout)|
|<|输入重定向(重定向stdin)|
|\||命令管道|
|&|重定向file descriptor或将命令至于后台(bg)运行|
|()|将其内部的命令置于nested subshell执行，或用于运算或变量替换|
|{}|将期内的命令置于non-named function中执行，或用在变量替换的界定范围|
|;|在前一个命令执行结束时，而忽略其返回值，继续执行下一个命令|
|&&|在前一个命令执行结束时，若返回值为true，继续执行下一个命令|
|\|\||在前一个命令执行结束时，若返回值为false，继续执行下一个命令|
|!|执行histroy列表中的命令|
|... | ...|

    
假如我们需要在`command line`中将这些保留元字符的功能关闭的话，
就需要quoting处理了。

在`bash`中，常用的quoting有以下三种方法：

- hard quote：''(单引号)，凡在hard quote中的所有meta均被关闭；
- soft quote：""(双引号)，凡在soft quote中大部分meta都会被关闭，但某些会保留(如$);
- escape: \ (反斜杠)，只有在紧接在escape(跳脱字符)之后的单一meta才被关闭；

> **Note:**

>  在soft quote中被豁免的具体meta清单，我不完全知道，
>  有待大家补充，或通过实践来发现并理解。


下面的例子将有助于我们对quoting的了解：
```shell
$ A=B C #空白符未被关闭，作为IFS处理
$ C：command not found.
$ echo $A

$ A="B C" #空白符已被关掉，仅作为空白符
$ echo $A
B C
```
在第一个给A变量赋值时，由于空白符没有被关闭，
`command line` 将被解释为：
`A=B 然后碰到<IFS>，接着执行C命令`
在第二次给A变量赋值时，由于空白符被置于soft quote中，
因此被关闭，不在作为`IFS`；
`A=B<space>C`
事实上，空白符无论在soft quote还是在hard quote中，
均被关闭。Enter键字符亦然：

```shell
$ A=`B
> C
> '
$ echo "$A"
B
C
```
在上例中，由于`enter`被置于hard quote当中，因此不再作为`CR`字符来处理。
这里的`enter`单纯只是一个断行符号(new-line)而已，
由于`command line`并没得到`CR`字符，
因此进入第二个`shell prompt`(`PS2`, 以>符号表示)，
`command line`并不会结束，直到第三行，
我们输入的`enter`并不在hard quote里面，
因此没有被关闭，
此时，`command line`碰到`CR`字符，于是结束，交给shell来处理。

上例的`Enter`要是被置于soft quote中的话，`CR`字符也会同样被关闭：

```shell
$ A="B
> C
> "
$ echo $A
B C
```
然而，由于 `echo $A`时的变量没有置于soft quote中，
因此，当变量替换完成后，并作命令行重组时，`enter`被解释为`IFS`，
而不是new-line字符。

同样的，用escape亦可关闭CR字符：
```shell
$ A=B\
> C\
>
$ echo $A
BC
```
上例中的，第一个`enter`跟第二个`enter`均被escape字符关闭了，
因此也不作为`CR`来处理，但第三个`enter`由于没有被escape，
因此，作为`CR`结束`command line`。
但由于`enter`键本身在shell meta中特殊性，在 \ escape字符后面
仅仅取消其`CR`功能， 而不保留其IFS功能。

你或许发现光是一个`enter`键所产生的字符，就有可能是如下这些可能：

- CR
- IFS
- NL(New Line)
- FF(Form Feed)
- NULL
- ...

至于，什么时候解释为什么字符，这个我就没法去挖掘了，
或者留给读者君自行慢慢摸索了...^-^


至于soft quote跟hard quote的不同，主要是对于某些meta的关闭与否，以$来做说明：
```shell
$ A=B\ C
$ echo "$A"
B C
$ echo '$A'
$A
```
在第一个`echo`命令行中，$被置于soft quote中，将不被关闭，
因此继续处理变量替换，
因此，`echo`将A的变量值输出到屏幕，也就是"B C"的结果。

在第二个`echo`命令行中，$被置于hard quote中，则被关闭，
因此，$只是一个$符号，并不会用来做变量替换处理，
因此结果是$符号后面接一个A字母：$A.


>  **练习与思考:** 如下结果为何不同？

>  tips: 单引号和双引号，在quoting中均被关闭了。


```shell
$ A=B\ C
$ echo '"$A"'  #最外面的是单引号
"$A"
$ echo "'$A'"  #最外面的是双引号
'B C'
```

------------------------------------
在CU的shell版里，我发现很多初学者的问题，
都与quoting的理解有关。
比方说，若我们在awk或sed的命令参数中，
调用之前设定的一些变量时，常会问及为何不能的问题。

要解决这些问题，关键点就是：**区分出 shell meta 与 command meta**

前面我们提到的那些meta，都是在command line中有特殊用途的，
比方说{}就是将一系列的command line置于不具名的函数中执行(可简单视为command block)，
但是，awk却需要用{}来区分出awk的命令区段(BEGIN,MAIN,END).
若你在command line中如此输入：
```shell
$ awk {print $0} 1.txt
```
由于{}在shell中并没有关闭，那shell就将{print $0}视为command block，
但同时没有`;`符号作命令分隔，因此，就出现awk语法错误结果。

要解决之，可用hard quote:
```shell
awk '{print $0}'
```
上面的hard quote应好理解，就是将原来的
{、<space>、$、}这几个shell meta关闭，
避免掉在shell中遭到处理，而完整的成为awk的参数中command meta。

> **Note:**

> awk中使用的$0 是awk中内建的field nubmer，而非awk的变量，
> awk自身的变量无需使用$.

要是理解了hard quote的功能，在来理解soft quote与escape就不难：
```shell
awk "{print \$0}" 1.txt
awk \{print \$0\} 1.txt
```
然而，若要你改变awk的$0的0值是从另一个shell变量中读进呢？
比方说：已有变量$A的值是0， 那如何在`command line`中解决
awk的$$A呢？
你可以很直接否定掉hard quote的方案：
```shell
$ awk '{print $$A}' 1.txt
```
那是因为$A的$在hard quote中是不能替换变量的。

聪明的读者(如你！)，经过本章的学习，我想，你应该可以理解为
为何我们可以使用如下操作了吧：
```shell
A=0
awk "{print \$$A}" 1.txt
awk  \{print\ \$$A\} 1.txt
awk '{print $'$A'}' 1.txt
awk '{print $'"$A"'}' 1.txt
```
或许，你能给出更多方案... ^_^

更多练习：
- http://bbs.chinaunix.net/forum/viewtopic.php?t=207178
  一个关于read命令的小问题：
  很早以前觉得很奇怪：执行read命令，然后读取用户输入给变量赋值，
  但如果输入是以空格键开始的话，这空格会被忽略，比如：
```shell
read a  #输入：    abc
echo "$a" #只输出abc
```
原因:
    变量a的值，从终端输入的值是以IFS开头，而这些IFS将被shell解释器忽略(trim)。
    应该与shell解释器分词的规则有关；

```shell
read a  #输入：\ \ \ abc
echo "$a" #只输出abc
```
需要将空格字符转义


> **Note:** 

>  IFS   Internal field separators, normally  space,  tab,  and newline (see Blank Interpretation section).
>  ......
>  Blank Interpretation
>   After parameter and command  substitution,  the  results  of substitution  
>   are scanned for internal field separator characters (those found in IFS) 
>   and split  into  distinct  arguments  where  such characters are found. 
>   Explicit null arguments ("" or  '')  are  retained.  
>   Implicit  null  arguments(those  resulting  from  parameters that have no values) 
>   are removed. 
>   (refre to: man sh)

解决思路：

1. shell command line 主要是将整行line给分解(break down)为每一个单词(word);
2. 而词与词之间的分隔符就是IFS (Internal Field Seperator)。
3. shell会对command line作处理(如替换，quoting等), 然后再按词重组。(注：别忘了这个重组特性)
4. 当你用IFS来事开头一个变量值，那shell会先整理出这个词，然后在重组command line。
5.然而，你将IFS换成其他，那shell将视你哪些space/tab为“词”，而不是IFS。那在重组时，可以得到这些词。

若你还是不理解，那来验证一下下面这个例子：
```shell
$ A="  abc" 
$ echo $A
abc
$ echo "$A" #note1
   abc
$ old_IFS=$IFS
$ IFS=;
$ echo $A
   abc
$ IFS=$old_IFS
$ echo $A
abc
```
> **Note:**

>  1. 这里是用 soft quoting 将里面的 space 关闭，使之不是 meta(IFS)，
>  而是一个literal(white space);

>  2. IFS=; 意义是将IFS设置为空字符，因为;是shell的元字符(meta);


问题二：为什么多做了几个分号，我想知道为什么会出现空格呢？
```shell
$ a=";;;test"                              
$ IFS=";"                                  
$ echo $a                                  
   test                                                                         
$ a="   test"                              
$ echo $a                                  
   test                                                                         
$ IFS=" "                                  
$ echo $a                                  
test    
```

解答：

这个问题，出在`IFS=;`上。
因为这个`;`在问题一中的command line上是一个meta,
并非`";"`符号本身。
因此，`IFS=;`是将IFS设置为 null charactor
(不是space、tab、newline)。

要不是试试下面这个代码片段：
```shell
$ old_IFS=$IFS
$ read A
;a;b;c
$ echo $A
;a;b;c
$ IFS=";"  #Note2
$ echo $A
a b c
```
> **Note:**

> 要关闭`;`可用`";"`或者`';'`或者`\;`。


- http://bbs.chinaunix.net/forum/viewtopic.php?t=216729

思考问题二：文本处理：读文件时，如何保证原汁原味。
```shell
cat file | while read i
do
   echo $i
done
```
文件file的行中包含若干空，经过read只保留不重复的空格。
如何才能所见即所得。

```shell
cat file | while read i
do
   echo "X${i}X"
done
```
从上面的输出，可以看出read，读入是按整行读入的;
不能原汁原味的原因：

1.  如果行的起始部分有IFS之类的字符，将被忽略;
2.  `echo $i`的解析过程中，首先将$i替换为字符串，
 然后对echo 字符串中字符串分词，然后命令重组，输出结果;
 在分词，与命令重组时，可能导致多个相邻的IFS转化为一个;

```shell
cat file | while read i
do
  echo "$i"
done
```
以上代码可以解决原因2中的，command line的分词和重组导致meta字符丢失；
但仍然解决不了原因1中，read读取行时，忽略行起始的IFS meta字符。

回过头来看上面这个问题：为何要原汁原味呢？
cat命令就是原汁原味的，只是shell的read、echo导致了某些shell的meta字符丢失;

如果只是IFS meta的丢失，可以采用如下方式：
将IFS设置为null，即`IFS=;`, 
在此再次重申此处`;`是shell的meta字符,而不是literal字符;
因此要使用literal的	`;`应该是`\;`
或者关闭meta 的(soft/hard) quoting的`";"`或者`';'`。

因此上述的解决方案是：
```shell
old_IFS=$IFS
IFS=; #将IFS设置为null
cat file | while read i
do
  echo "$i"
done
IFS=old_IFS #恢复IFS的原始值
```
现在，回过头来看这个问题，为什么会有这个问题呢；
其本源的问题应该是没有找到解决原始问题的最合适的方法，
而是采取了一个迂回的方式来解决了问题；

因此，我们应该回到问题的本源，重新审视一下，问题的本质。
如果要精准的获取文件的内容，应该使用od或者hexdump会更好些。




















