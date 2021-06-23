# 9 是否可以更改 POSIX 标准以简化文件处理?

The POSIX standard could (and should!) be modified to make it easier to to handle the outrageously permissive pathnames that are permitted today. Basically, we need extensions to make globbing and find easier to use.

## 9.1 Globbing
There are two basic problems with globbing:

1. Globbing in shell returns junk (the pattern) when there are zero matches. There should be a shell option (typically called a "nullglob" option) so an empty list is returned if nothing matches and there was at least one metacharacter. Oddly enough, the underlying glob() function has an option that’s close to this, but there’s no standard way for shells to take advantage of it! Bash, ksh, and others have support, but not in a common standard way, and glob() doesn’t support exactly what is needed either (bugid:247).
2. Globbing normally replies pathnames beginning with "-". There should be an option that when set prepends "./" to any glob result that begins with "-". This should be an option for glob(), as well as for the shell. I think the standard should also state that implementations may enable this by default. Not all real-world commands support "--", and users often forget to add it; we need to have a mechanism to automatically deal with pathnames beginning with "./" if you need them.

## 9.2 Find / null separators
There also needs to be standard way to use find with arbitrary pathnames. The normal way to handle this is by separating pathnames with the null (\0) character; a few changes would simplify this:

1. Extend existing commands to generate or use null-separated pathname lists. At the least, add "find -print0" (bugid:243) and "xargs -0" (bugid:244) since these are already widely implemented. For consistency, I think "-0" should be the standard option name for null-separated lists. It’d be useful to add "grep -0" (GNU grep accepts either -Z or --null) and "sort -0" (GNU sort uses -z). Once added, this common template would be portable:

```shell
 find . -print0 | xargs -0 COMMAND
```

2. Extend the shell’s read so that it can easily read null-separated streams. (bugid:245) Bash can do this today, but it’s painful; the command is IFS="" read -d "" -r which is overly complicated I believe there should be a new "-0" option for read, which says "ignore IFS, and just read until the next \0 byte" (Here’s a bash 4.1 patch). You can then do this (which makes it easier to have long command sequences, as long as you don’t need stdin):

```shell
 find . -print0 | while read -0 file ; do ... done
```

3. Extend the shell so that its for loop can handle a null-separated list. This one is harder; it’s not obvious how to do this. My current theory is that there be a new shell option 'nullseparator"; when enabled, IFS is ignored, and instead \0 is the input seperator. Then, extend the shell’s for loop syntax so that if you say then in instead of in, this mode is temporarily enabled while the list is processed (the original setting is then restored). (I originally had null in, but using then in means that no new keywords are needed.) You could then do this:

```shell
 for file then in $(find . -print0) do ... done
```

As a side note, it’d be nice if the $'...' construct was standard, as it makes certain things easier (bugid:249).
