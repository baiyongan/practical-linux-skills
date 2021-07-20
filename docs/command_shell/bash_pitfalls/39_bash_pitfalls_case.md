# Bash Pitfalls Case 39
## expr sub-string fails for "match"

This works reasonably well

---

most of the time

```shell
word=abcde
expr "$word" : ".\(.*\)"
bcde
```

But WILL fail for the word "match"

```shell
word=match
expr "$word" : ".\(.*\)"
```

The problem is "match" is a keyword. Solution (GNU only) is prefix with a '+'

```shell
word=match
expr + "$word" : ".\(.*\)"
atch
```

Or, y'know, stop using expr. You can do everything expr does by using Parameter Expansion. What's that thing up there trying to do? Remove the first letter of a word? That can be done in POSIX shells using PE or Substring Expansion:

```shell
$ word=match
$ echo "${word#?}"    # PE
atch
$ echo "${word:1}"    # SE
atch
```

Seriously, there's no excuse for using expr unless you're on Solaris with its non-POSIX-conforming /bin/sh. It's an external process, so it's much slower than in-process string manipulation. And since nobody uses it, nobody understands what it's doing, so your code is obfuscated and hard to maintain.