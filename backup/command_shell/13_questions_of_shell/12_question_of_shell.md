## shell十三问之12：你要if还是case呢？
--------------------------------------

还记得我们在第10章所介绍的`return value`吗？

是的，接下来的介绍的内容与之有关，
若你的记忆也被假期所抵消的话，
那建议您还是回去温习温习再回来...

若你记得`return value`，我想你也应该记得了
`&&` 与 `||` 什么意思吧?
用这两个符号再搭配 command group的话，
我们可让shell script变得更加聪明哦。
比方说：
```shell
cmd1 && {
    cmd2
	cmd3
	;
} || {
	cmd4
	cmd5
}
```
意思是说：
若 cmd1的`return value`为true的话，
然后执行cmd2与cmd3，
否则执行cmd4与cmd5.

事实上， 我们在写shell script的时候，
经常需要用到这样、那样的条件
以作出不同的处理动作。
用`&&`与`||`的确可以达成条件执行的结果，
然而，从“人类语言”上来理解，
却不是那么直观。
更多时候，我们还是喜欢用`if...then...else...`
这样的的keyword来表达条件执行。

在bash shell中，我们可以如此修改上一段代码：
```shell
if cmd1
then
	cmd2
	cmd3
else
	cmd4
	cmd5
fi
```

这也是我们在shell script中最常用的`if`判断式：
 只要`if`后面的command line返回true的return value
 (我们常用`test`命令返回的return value)，
 然则就执行`then`后面的命令，否则，执行`else`之后的命令，
 `fi`则是用来结束判断式的keyword。


 在`if`的判断式中，`else`部分可以不用，但`then`是必需的。
 (若`then`后不想跑任何command，可用`:`这个`null command`代替)。
 当然，then或else后面，也可以再使用更进一层的条件判断式，
 这在shell script的设计上很常见。
 若有多项条件需要"依序"进行判断的话，
 那我们则可使用`elif`这样的keyword：
 
```shell
if cmd1; then
    cmd2;
elif cmd3; then
 	cmd4
else
 	cmd5
fi
```
 意思是说：
 	若cmd1为true，然则执行cmd2；
 	否则在测试cmd3，若为true则执行cmd4；
 	倘若cmd1与cmd3均不成立，那就执行cmd5。

 `if`判断式的例子很常见，你可从很多shell script中
 看得到，我这里不再举例子了...

 接下来为要为大家介绍的是`case`判断式。
 虽然`if`判断式已可应付大部分的条件执行了，
 然而，在某些场合中，却不够灵活，
 **尤其是在string式样的判断上**，比方如下：

```shell
QQ() {
    echo -n "Do you want to continue? (Yes/No): "
 	read YN
 	if [ "$YN" = Y -o "$YN" = y -o "$YN" = "Yes" -o "$YN" = "yes" -o "$YN" = YES]
 	then
 		QQ
 	else
 		exit 0
 	fi	
}

QQ
```

从例中，我们看得出来，
最麻烦的部分是在判断YN的值可能有好几种样式。

聪明的你或许会如此修改：
```shell
 QQ() {
 	echo -n "Do you want to continue? (Yes/No): "
 	read YN
 	if echo "$YN" | grep -q '^[Yy]\([Ee][Ss]\)*$'
 	then
 		QQ
 	else
 		exit 0
 	fi	
}

QQ
```
也就是用`Regular Expression`来简化代码。
(我们有机会，再来介绍`RE`)
只是...是否有其他更方便的方法呢？
有的，就是用`case`判断式即可：
```shell
 QQ() {
 	echo -n "Do you want to continue? (Yes/No): "
 	read YN
 	case "$YN" in
 		[Yy]|[Yy][Ee][Ss])
 			QQ
 			;;
 		*)
 			exit 0
 			;;
 	esac
}

QQ
```
我们常用的`case`的判断式来判断某一变量
在不同的值(通常是string)时，作出不同的处理，
比方说， **判断script参数，以执行不同的命令**。

若你有兴趣，且用linux系统的话，
不妨挖一挖`/etc/init.d/*`中的那堆script中的`case`用法.
如下就是一例：
```shell
case "$1" in
	start)
		start
		;;
	stop)
		stop
		;;
	status)
		rhstatus
		;;
	restart|reload)
		restart
		;;
	condrestart)
		[ -f /var/lock/subsys/syslog ] && restart || :
		;;

	*)
		echo $"Usage: $0 {start|stop|status|restart|condrestart}"
		exit 1
esac
```
(若你对 postional parameter的印象已经模糊了，请重看第9章吧。)

okay，是十三问还剩一问而已，过几天再来搞定之...^_^
