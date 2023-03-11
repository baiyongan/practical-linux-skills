# Bash Pitfalls Case 51
## Non-atomic writes with xargs -P

GNU xargs supports running multiple jobs in parallel. -P n where n is the number of jobs to run in parallel.

```shell
seq 100 | xargs -n1 -P10 echo "$a" | grep 5
seq 100 | xargs -n1 -P10 echo "$a" > myoutput.txt
```

This will work fine for many situations but has a deceptive flaw: If $a contains more than 8192 characters (the limit depends on platform and version), the echo may not be atomic (it may be split into multiple write() calls), and there is a risk that two lines will be mixed.

```shell
$ perl -e 'print "a"x10000, "\n"' > foo
$ strace -e write bash -c 'read -r foo < foo; echo "$foo"' >/dev/null
write(1, "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"..., 8192) = 8192
write(1, "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"..., 1809) = 1809
+++ exited with 0 +++
```

Obviously the same issue arises if there are multiple calls to echo or printf:

```shell
slowprint() {
  printf 'Start-%s ' "$1"
  sleep "$1"
  printf '%s-End\n' "$1"
}
export -f slowprint
seq 10 | xargs -n1 -I {} -P4 bash -c "slowprint {}"
# Compare to no parallelization
seq 10 | xargs -n1 -I {} bash -c "slowprint {}"
# Be sure to see the warnings in the next Pitfall!
```

Outputs from the parallel jobs are mixed together, because each job consists of two (or more) separate write() calls.

If you need the outputs unmixed, it is therefore recommended to use a tool that guarantees output will be serialized (such as GNU Parallel).

For further details see a demonstration of the mixing problem.