# Bash Pitfalls Case 22
## cmd1 && cmd2 || cmd3

有些人尝试使用 `&&` 和 `||` 作为 `if ... then ... else ... fi` 的快捷语法，也许是因为他们认为自己很聪明。 例如，

```shell
 # WRONG!
 [[ -s $errorlog ]] && echo "Uh oh, there were some errors." || echo "Successful."
```

但是，在一般情况下，此构造并不完全等同于 `if ... fi`。 `&&` 之后的命令也会生成退出状态，如果该退出状态不是“真”（0），则 `||` 之后的命令 也会被调用。 例如：

```shell
 i=0
 true && ((i++)) || ((i--))  # WRONG!
 echo "$i"                   # Prints 0
```

这里发生了什么？ 看起来 i 应该是 1，但结果是 0。为什么？ 因为 i++ 和 i-- 都被执行了。 `((i++))` 命令具有退出状态，并且该退出状态源自对括号内表达式的类 C 评估。 该表达式的值恰好是 0（i 的初始值），而在 C 中，整数值为 0 的表达式被认为是假的。 因此 `((i++))` （当 i 为 0 时）的退出状态为 1 (false)，因此 `((i--))` 命令也被执行。

另一个聪明人认为我们可以通过使用预增量运算符来修复它，因为 ++i 的退出状态（i 最初为 0）为真：

```shell
 i=0
 true && (( ++i )) || (( --i ))  # STILL WRONG!
 echo "$i"                       # Prints 1 by dumb luck
```

但这遗漏了这个例子的重点。 它只是巧合，你不能依赖 `x && y || z` 如果 y 有任何失败的机会！（如果我们将 i 初始化为 -1 而不是 0，这个例子仍然失败。）

如果您需要安全，或者您只是不确定它是如何工作的，或者如果前面段落中的任何内容不完全清楚，请在您的程序中使用简单的 `if ... fi` 语法。

```shell
 i=0
 if true; then
   ((i++))
 else
   ((i--))
 fi
 echo "$i"    # Prints 1
```

本节也适用于 Bourne shell，这是说明它的代码：

```shell
 # WRONG!
 true && { echo true; false; } || { echo false; true; }
```

输出是两行“真”和“假”，而不是单行“真”。
