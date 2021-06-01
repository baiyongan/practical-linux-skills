# IFS 分隔符

## IFS 功能介绍
IFS (Internal Field Seperator) 是在 Linux 的 shell 中预设的分隔符，用来把 command line 分解成 word（字段）。
IFS 可以是 White Space（空白键）、Tab（ 表格键）、Enter（ 回车键）中的一个或几个。如果bash shell 在数据中看到这些字符中的任意一个，它就会假定你在列表中开始了新的数据段。
它在处理文本数据时，非常有用。

```shell
# manual 中查询 IFS 介绍
man bash 
IFS    The Internal Field Separator that is used for word splitting after expansion and to split lines into words  with  the  read
       builtin command.  The default value is ‘‘<space><tab><newline>’’.
# set 中查询默认的 IFS 设置
[bya@cluster007 test]$ set | grep ^IFS
IFS=$' \t\n'
```

## IFS 自定义设置方式 

- 如果要将文本数据中的一行作为一个数据段，那么可以修改IFS的值使其只能识别换行符，即：
```shell
IFS=$'\n'
```
- 如果要遍历一个文件（如/etc/passwd文件）中用冒号分隔的值，可以将IFS值设为冒号：
```shell
IFS=:
```
- 如果要指定多个IFS字符，只要将它们的串起来即可：
```shell
IFS=$'\n:;"'  #这个赋值是将换行符、冒号、分号和双引号作为字段分隔符
```

## IFS 在脚本编程中的使用

在处理长脚本时，可能在一个地方修改了 IFS 值，然后忘记它了并在其他地方以为是默认值。因此，需要在改变 IFS 之前保存原来的 IFS 值。
```shell
IFS.OLD=$IFS
IFS=$'\n'
#<use the new IFS value in code>
#······
IFS=$IFS.OLD  
```

## 参考资料

[IFS in man bash](https://man7.org/linux/man-pages/man1/bash.1.html)

[详细解析 Shell 中的 IFS 变量](https://blog.csdn.net/guyongqiangx/article/details/80220434)

[linux中分隔符IFS](https://blog.csdn.net/earthchinagl/article/details/80804893)

[The Meaning of IFS in Bash Scripting](https://www.baeldung.com/linux/ifs-shell-variable)