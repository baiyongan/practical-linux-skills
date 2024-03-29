## shell十三问之9：$@与$*差在哪？
------

要说$@与$*之前，
需得先从shell script的positional parameter谈起...

我们都已经知道变量(variable)是如何定义和替换的，
这个不再多讲了。

#### 1. shell script的positional parameter
--------------

但是，我们还需要知道有些变量是shell内定的，
且其名称是我们不能随意修改的。
其中，就有positional parameter在内。

在shell script中，我们可用$0, $1, $2, $3 ...
这样的变量分别提取命令行中的如下部分:
```shell
script_name parameter1 parameter2 parameter3 ...
```

我们很容易就能猜出, `$0`就是代表 shell script名称(路径)本身，
而`$1`就是其后的第一个参数，如此类推...

须得留意的是`IFS`的作用, 也就是`IFS`被quoting处理后，
那么positional parameter也会改变。

如下例：
```shell
my.sh p1 "p2 p3" p4
```
由于p2与p3之间的空白键被soft quoting所关闭了，
因此，my.sh的中$2是"p2 p3",而$3则是p4...

还记得前两章，我们提到function时，
我们不是说过，它是script中的script吗？^_^

是的，function一样可以读取自己的(有别于script的)
positional parameter, 唯一例外的是$0而已。

举例而言：
假设my.sh里有一个函数(function)叫my_fun,
若在script中跑`my_fun fp1 fp2 fp3`,
那么，function内的$0就是my.sh，而$1是fp1而不是p1了...

不如写个简单的my.sh script 看看吧：
```shell
#!/bin/bash

my_fun() {
    echo '$0 inside function is '$0
	echo '$1 inside function is '$1
	echo '$2 inside function is '$2
}

echo '$0 outside function is '$0
echo '$1 outside function is '$1
echo '$2 outside function is '$2

my_fun fp1 "fp2 fp3" 
```
然后在command line中跑一下 script就知道了：
```shell
chmod 755 my.sh

./my.sh p1 "p2 p3"
$0 outside function is ./my.sh
$1 outside function is p1
$2 outside function is p2 p3
$0 inside function is ./my.sh
$1 inside function is fp1
$2 inside function is fp2 fp3
```

然而，在使用positional parameter的时候，
我们要注意一些陷阱哦：

**$10不是替换第10个参数，
而是替换第一个参数，然后在补一个0于其后;**

也就是说， `my.sh one two three four five six seven eight nine ten`
这样的command line, my.sh里的$10不是ten而是one0 哦...小心小心
要抓到ten的话，有两种方法：

- 方法一：使用我们上一章介绍的${}, 也就是用${10}即可。

- 方法二：就是shift了。

用通俗的说法来说，
**所谓的shift就是取消positional parameter中最左边的参数($0不受影响)**。
其预设值为1，也就是shift 或shift 1 都是取消$1,
而原本的$2则变成$1, $3则变成$2...
那亲爱的读者，你说要shift掉多少个参数，
才可用$1取得到${10} 呢？ ^_^

okay，当我们对positional parameter有了基本的概念之后，
那再让我们看看其他相关变量吧。


#### 2. shell script的positional parameter的number
--------------

先是$#, 它可抓出positional parameter的数量。
以前面的`my.sh p1 "p2 p3"`为例：
由于"p2 p3"之间的`IFS`是在soft quote中，
因此，$#就可得到的值是2.
但如果p2与p3没有置于quoting中话，
那$#就可得到3的值了。
同样的规则，在function中也是一样。

因此，我们常在shell script里用如下方法，
测试script是否有读进参数：
```shell
[ $# = 0 ]
```
假如为0, 那就表示script没有参数，否则就是带有参数...

#### 3. shell script中的$@与$*
-------------

接下来就是**$@与$*:
精确来讲，两者只有在soft quote中才有差异，
否则，都表示“全部参数” ($0除外)**。

若在comamnd line上， 跑`my.sh p1 "p2 p3" p4`的话，
不管$@还是$\*, 都可得到 p1 p2 p3 p4就是了。

但是，**如果置于soft quote中的话：**

- **"$@"则可得到 "p1" "p2 p3" "p4" 这三个不同字段(word);**
- **"$*"则可得到 "p1 p2 p3 p4" 这一整个单一的字段。**

我们修改一下前面的my.sh，使之内容如下：
```shell
#!/bin/bash

my_fun() {
	echo "$#"
}

echo 'the number of parameter in "$@" is ' $(my_fun "$@")
echo 'the number of parameter in "$*" is ' $(my_fun "$*")

```
然后再执行:
```shell
./my.sh p1 "p2 p3" p4
```
就知道，$@与$*差在哪了... ^_^
