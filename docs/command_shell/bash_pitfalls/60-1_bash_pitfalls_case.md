# Bash Pitfalls Case 60-1
## errexit

errexit (set -e) tries to abort the script when an error occurs, which sounds like a good idea at first, but it has very intricate rules regarding when to abort on error or not. Some of the main problems with errexit are

It's not actually possible for the shell to detect errors. All it has to go on is a command's exit status. When commands fail, they normally return a non-zero exit status, but many commands also use the exit status to convey a true/false value. Examples of such commands are test, [, [[ ... ]], ((...)), and grep.

When a command you test with if or && or || is a function, set -e ignores non-zero exit statuses of commands in that function. Consider a function like

```shell
# WRONG
cleanup() {
  cd "$1"
  rm -f ./*
}
```

If that cd command fails, you definitely don't want that rm command to run, and with a simple use of the function when errexit is enabled, that happens to be the case:

```shell
set -e
cleanup() {
  cd "$1"
  printf 'Oops!\n'
}
cleanup /no/longer/there
# scriptname: cd: /no/longer/there: No such file or directory
```

But then later you decide to add a custom error message

```shell
cleanup /no/longer/there || {
  printf >&2 'Cleanup failed\n'
  exit 1
}
# scriptname: cd: /no/longer/there: No such file or directory
# Oops!
```

cd is being run in a context where it's tested, even though it's not at all apparent inside the function definition where the line is located. The correct way, with or without errexit, is to explicitly check if cd failed or not

```shell
# Right
cleanup() {
  cd "$1" || return
  rm -f ./*
}
```

Command substitution disables errexit. Consider the following example function that mimics a realpath command:

```shell
set -e
realdir() {
  cd "$1"
  pwd -P
}
realdir /no/such/dir
# scriptname: cd: /no/such/dir
```

It aborted on the failing cd command, but if you try to capture its output, then cd's failure is ignored

```shell
dir=$(realdir /no/such/dir)
# scriptname: cd: /no/such/dir
printf 'dir is <%s>\n' "$dir"
# dir is </home/user>
```

The correct way is again to explicitly check if cd failed or not, regardless of whether you use errexit

```shell
# Right
realdir() {
  cd "$1" || return
  pwd -P
}
```

See Why doesn't set -e (or set -o errexit, or trap ERR) do what I expected?.