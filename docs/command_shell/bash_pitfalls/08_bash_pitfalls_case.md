# Bash Pitfalls Case 08
## grep foo bar | while read -r; do ((count++)); done

上面的代码乍一看还不错，不是吗？ 当然，这只是 grep -c 的一个糟糕的实现，但它只是一个简单的例子。 对 count 的更改不会传播到 while 循环之外，因为管道中的每个命令都在单独的 SubShell 中执行。 这在某些时候，几乎让每个 Bash 初学者都感到惊讶。

POSIX 没有指定管道的最后一个元素是否在子 shell 中计算。 某些 shell，例如 ksh93 和启用了 shopt -s lastpipe 的 Bash >= 4.2， 将在原始 shell 进程中运行此示例中的 while 循环，从而允许其中的任何副作用生效。 因此，必须以不依赖任何一种行为的方式，来编写可移植脚本。

有关此问题和类似问题的解决方法，请参阅 Bash FAQ #24。 放在这里有点太长了。

