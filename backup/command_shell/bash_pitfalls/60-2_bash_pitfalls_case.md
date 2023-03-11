# Bash Pitfalls Case 60-2
## pipefail

Normally, the exit status of a pipeline is the exit status of the last command of that pipeline. With pipefail, if any part of the pipeline returns non-zero, the whole pipeline returns non-zero. This may sound like a good idea, but it's actually normal for commands earlier in a pipeline to exit non-zero without it being an error. Consider the following example

```shell
if some_command | grep -q foo; then
   printf 'foo found\n'
else
   printf 'foo not found\n'
fi
```

If pipefail is enabled, the above will sometimes claim "foo not found" even when foo was found.

grep -q foo returns 0 to indicate that at least one line matches the pattern, else it returns 1 to indicate that no lines matched the pattern. Since -q (quiet) causes grep to not output the matching lines, it doesn't actually need to read the whole stream; it's enough to find at least one line that matches the pattern. A typical grep implementation will thus exit with status 0 as soon as it found the first matching line.

The some_command on the other end of the pipeline will not know that. It only sees its end of the pipe, it does not see what is on the other end. When it tries to write the next chunk of data to the pipe, and the pipe is closed in the other end, the system will send some_command a SIGPIPE signal to tell it that it can no longer write to the pipe. The default action to take when SIGPIPE is received is to exit with a non-zero status. This means that the whole pipeline returns non-zero, and the else branch is executed, even though it successfully found a matching line.

If you know that each part of a pipeline beyond the first will consume all their input, pipefail is safe and often a good choice, but it should not be enabled globally, only enabled for pipelines where it makes sense, and disabled afterwards.