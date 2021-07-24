# Bash Pitfalls Case 21
## for i in {1..10}; do ./something &; done

You cannot put a ; immediately after an &. Just remove the extraneous ; entirely.

你不能放一个 `;` 紧跟在 `&` 之后。 只需完全删除无关的 `；`。

```shell
 for i in {1..10}; do ./something & done
```

或者:
```shell
 for i in {1..10}; do
   ./something &
 done
```

`&` 已经用作命令终止符，就像 `;` 一样。 所以不能将两者混合。

一般来说，一个 `;` 可以用换行符代替，但不是所有的换行符都可以用 `;` 代替。


