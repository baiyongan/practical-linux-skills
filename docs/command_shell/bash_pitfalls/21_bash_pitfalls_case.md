# Bash Pitfalls Case 21
## for i in {1..10}; do ./something &; done

You cannot put a ; immediately after an &. Just remove the extraneous ; entirely.

```shell
 for i in {1..10}; do ./something & done
```

Or:
```shell
 for i in {1..10}; do
   ./something &
 done
```

& already functions as a command terminator, just like ; does. And you cannot mix the two.

In general, a ; can be replaced by a newline, but not all newlines can be replaced by ;.


