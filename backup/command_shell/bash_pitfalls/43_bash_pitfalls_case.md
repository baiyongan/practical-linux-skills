# Bash Pitfalls Case 43
## somecmd 2>&1 >>logfile

This is by far the most common mistake involving redirections, typically performed by someone wanting to direct both stdout and stderr to a file or pipe will try this and not understand why stderr is still showing up on their terminal. If you're perplexed by this, you probably don't understand how redirections or possibly file descriptors work to begin with. Redirections are evaluated left-to-right before the command is executed. This semantically incorrect code essentially means: "first redirect standard error to where standard out is currently pointing (the tty), then redirect standard out to logfile". This is backwards. Standard error is already going to the tty. Use the following instead:

```shell
somecmd >>logfile 2>&1
```

See a more in-depth explanation, Copy descriptor explained, and BashGuide - redirection.