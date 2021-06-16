# Bash Pitfalls Case 20
## [ bar == "\$foo" ]

The == operator is not valid for the POSIX [ command. Use = or the [[ keyword instead.

```shell
 [ bar = "$foo" ] && echo yes
 [[ bar == $foo ]] && echo yes
```

In Bash, [ "$x" == y ] is accepted as an extension, which often leads Bash programmers to think it's the correct syntax. It's not; it's a Bashism. If you're going to use Bashisms, you might as well just use [[ instead.