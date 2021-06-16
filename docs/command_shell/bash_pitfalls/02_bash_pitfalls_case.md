# Bash Pitfalls Case 02
## cp \$file \$target

上面显示的命令有什么问题？ 好吧，什么都没有，前提是如果您事先知道 $file 和$target 中没有空格（并且您没有修改$ IFS，同时可以保证在$IFS可能被修改的上下文中，不调用该代码）或[通配符][1]。 然而事实上，变量扩展的结果仍受[WordSplitting][2] 和 [pathname expansion][3] 的影响。 所以对于参数扩展，请始终使用双引号。

```shell
cp -- "$file" "$target"
```

没有双引号扩住的话，您将得到类似如下的错误
```shell
cp 01 - Don't Eat the Yellow Snow.mp3 /mnt/usb

cp: cannot stat `01': No such file or directory.
```
如果 $file 中包含通配符（*或？或\[），则如果存在匹配它们的文件，它们将被[扩展][4]。 
使用双引号扩住变量的话，一切安好，除非 “$file” 恰好以 - 开头，在这种情况下，cp 命令会认为您正在尝试使用命令行选项（请参见下面的[陷阱3][5]）。

即使在一些少见的情况下，你可以保证变量的具体内容，但用双引号扩住参数扩展（尤其是对文件名变量进行引用）仍然是常规且良好的做法。

除非在少数情况下，也就是——能够从即时的代码上下文片段中，完全明显地知晓某个参数，一定会是一个安全值，否则，有经验的脚本编写者会始终使用引号。脚本专家很可能会认为标题中的 cp命令是错误的写法，你也应该这样认为哦。


## 拓展阅读
[quotes](http://mywiki.wooledge.org/Quotes)

[1]: http://mywiki.wooledge.org/glob
[2]: http://mywiki.wooledge.org/WordSplitting
[3]: http://mywiki.wooledge.org/glob
[4]: http://mywiki.wooledge.org/glob
[5]: http://mywiki.wooledge.org/BashPitfalls#pf3