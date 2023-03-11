# Bash Pitfalls Case 56
## Using xargs without -0

xargs splits on whitespace. This is unfortunate because whitespace is allowed in filenames and commonly used by GUI users. xargs also treats ' and " specially, which can also lead to problems:

```shell
touch Dad\'s\ \"famous\"\ 1\'\ pizza.txt
touch Dad\'s\ 12\"\ records.txt
touch 2\"x1\'\ wood.txt
touch 2\"x4\"\ wood.txt
```

Here xargs warns:

```shell
# Do not do this
$ find . -type f | xargs wc
xargs: unmatched single quote; by default quotes are special to xargs unless you use the -0 option
```

Here xargs does not warn at all:

```shell
# Do not do this
echo * | xargs wc
find *famous* -type f | xargs wc
find *4* -type f | xargs wc
```

Instead use xargs -0:

```shell
# Do this instead
printf '%s\0' * | xargs -0 wc
find . -type f -name '*famous*' -print0 | xargs -0 wc
find . -type f -name '*4*' -exec wc {} +
```

If using -0 is not simple, an alternative is to use GNU Parallel, which splits on \n. And while \n is also allowed in filenames they never occur unless your users are malicious. In any case: If you use xargs without -0 put a comment in your code explaining why that is safe in your particular situation.


