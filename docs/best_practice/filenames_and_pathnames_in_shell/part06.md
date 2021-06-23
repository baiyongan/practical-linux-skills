# 6 使用以空分割的路径名

If you want to exchange pathnames (in their full generality) between programs, or store them for later, a common solution is to use byte 0 (aka \0 or null) to separate pathnames. This works because pathnames, by definition, cannot include byte 0.

This is very useful, and it works nicely, but note that there are downsides to this approach:

- The easy way to use this convention requires the use of nonstandard extensions that are not a part of the POSIX standard, so the result is non-standard and less portable. Still, many toolsets have a few tool extensions to support this, including the GNU, *BSD, and busybox toolsets. Hopefully someday POSIX will add this.
- The option names to use this convention today (when available) are jarringly inconsistent. In particular, perl uses -0, while the GNU tools options are as follows: sort -z, find -print0, xargs -0, and grep accepts either -Z or --null. The equivalent option in the bash shell read command is -d "" (aka empty delimeter).
- This convention is supported by only a few tools (e.g., the GNU toolset includes support, but in only a few of its tools).
- This format is more difficult to view and modify, in part because so few tools support it, compared to the line-at-a-time format that is widely supported.
- Most shells cannot store byte 0 in a variable at all. You can’t even pass such null-separated lists back to the shell via command substitution; cat $(find . -print0) and similar "for" loops don’t work. Even the POSIX standard’s version of "read" can’t use \0 as the separator (POSIX’s read has the -r option, but not bash’s -d option).
But if you want maximum generality when recursing into subdirectories, this is a common and relatively painless way to do it.
