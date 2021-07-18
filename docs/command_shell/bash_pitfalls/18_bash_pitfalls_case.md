# Bash Pitfalls Case 18
## su -c 'some command'

这个语法几乎是正确的。 问题是，在许多平台上， `su` 需要一个 `-c` 参数，但这不是你想要的。 例如，在 OpenBSD 上：

```shell
 $ su -c 'echo hello'
 su: 只有超级用户可以指定登录类
```

若想将 `-c 'some command'` 传递给 shell，这意味着你需要在 `-c` 之前输入用户名。

```shell
 su root -c 'some command' # 正确写法
```

当你省略一个用户名时，｀su｀ 假定用户名是 root，但是当你想在之后将命令传递给 shell 时，这会回在它的面前。 在这种情况下，你必须提供用户名。
