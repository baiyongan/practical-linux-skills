# Bash Pitfalls Case 24
## for arg in \$*

Bash (like all Bourne shells) has a special syntax for referring to the list of positional parameters one at a time, and $* isn't it. Neither is $@. Both of those expand to the list of words in your script's parameters, not to each parameter as a separate word.

The correct syntax is:

```shell
 for arg in "$@"

 # Or simply:
 for arg
```

Since looping over the positional parameters is such a common thing to do in scripts, for arg defaults to for arg in "$@". The double-quoted "$@" is special magic that causes each parameter to be used as a single word (or a single loop iteration). It's what you should be using at least 99% of the time.

Here's an example:

```shell
 # Incorrect version
 for x in $*; do
   echo "parameter: '$x'"
 done

 $ ./myscript 'arg 1' arg2 arg3
 parameter: 'arg'
 parameter: '1'
 parameter: 'arg2'
 parameter: 'arg3'
```

It should have been written:

```shell
 # Correct version
 for x in "$@"; do
   echo "parameter: '$x'"
 done
# or better:
 for x do
   echo "parameter: '$x'"
 done

 $ ./myscript 'arg 1' arg2 arg3
 parameter: 'arg 1'
 parameter: 'arg2'
 parameter: 'arg3'
 ```