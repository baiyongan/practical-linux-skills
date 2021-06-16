# Bash Pitfalls Case 14
## echo \$foo

This relatively innocent-looking command causes massive confusion. Because the $foo isn't quoted, it will not only be subject to WordSplitting, but also file globbing. This misleads Bash programmers into thinking their variables contain the wrong values, when in fact the variables are OK -- it's just the word splitting or filename expansion that's messing up their view of what's happening.

```shell
 msg="Please enter a file name of the form *.zip"
 echo $msg
```

This message is split into words and any globs are expanded, such as the *.zip. What will your users think when they see this message:

```shell
 Please enter a file name of the form freenfss.zip lw35nfss.zip
```

To demonstrate:

```shell
 var="*.zip"   # var contains an asterisk, a period, and the word "zip"
 echo "$var"   # writes *.zip
 echo $var     # writes the list of files which end with .zip
```

In fact, the echo command cannot be used with absolute safety here. If the variable contains -n for example, echo will consider that an option, rather than data to be printed. The only absolutely sure way to print the value of a variable is using printf:

```shell
 printf "%s\n" "$foo"
```
