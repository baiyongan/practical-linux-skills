# Bash Pitfalls Case 19
## cd /foo; bar

If you don't check for errors from the cd command, you might end up executing bar in the wrong place. This could be a major disaster, if for example bar happens to be rm -f *.

You must always check for errors from a cd command. The simplest way to do that is:

```shell
 cd /foo && bar
```

If there's more than just one command after the cd, you might prefer this:

```shell
 cd /foo || exit 1
 bar
 baz
 bat ... # Lots of commands.
```

cd will report the failure to change directories, with a stderr message such as "bash: cd: /foo: No such file or directory". If you want to add your own message in stdout, however, you could use command grouping:

```shell
 cd /net || { echo >&2 "Can't read /net. Make sure you've logged in to the Samba network, and try again."; exit 1; }
 do_stuff
 more_stuff
```

Note there's a required space between { and echo, and a required ; before the closing }. You could also write a die function, if you prefer.

Some people also like to enable set -e to make their scripts abort on any command that returns non-zero, but this can be rather tricky to use correctly (since many common commands may return a non-zero for a warning condition, which you may not want to treat as fatal).

By the way, if you're changing directories a lot in a Bash script, be sure to read the Bash help on pushd, popd, and dirs. Perhaps all that code you wrote to manage cd's and pwd's is completely unnecessary.

Speaking of which, compare this:

```shell
 find ... -type d -print0 | while IFS= read -r -d '' subdir; do
   here=$PWD
   cd "$subdir" && whatever && ...
   cd "$here"
 done
```

With this:

```shell
 find ... -type d -print0 | while IFS= read -r -d '' subdir; do
   (cd "$subdir" || exit; whatever; ...)
 done
```

Forcing a SubShell here causes the cd to occur only in the subshell; for the next iteration of the loop, we're back to our normal location, regardless of whether the cd succeeded or failed. We don't have to change back manually, and we aren't stuck in a neverending string of ... && ... logic preventing the use of other conditionals. The subshell version is simpler and cleaner (albeit a tiny bit slower).

Another approach is to cd unconditionally to where we're supposed to be, at the start of each loop iteration:

```shell
 here=$PWD
 find ... -type d -print0 | while IFS= read -r -d '' subdir; do
    cd "$here" || continue
    cd "$subdir" || continue
    whatever
    ...
 done
```

At least this way, we can continue to the next loop iteration and don't have to string an indefinite series of && together to ensure that we reach the cd at the end of the loop body.


