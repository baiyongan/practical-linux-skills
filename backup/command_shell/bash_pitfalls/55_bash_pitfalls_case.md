# Bash Pitfalls Case 55
## myprogram 2>&-

Do not close stdin, stdout or stderr as a "shorthand" for redirecting to /dev/null. Write it out correctly.

```shell
myprogram 2>/dev/null
```

Why? Consider what happens when your program tries to write an error message to stderr. If stderr has been redirected to /dev/null, the write succeeds, and your program is free to carry on, secure in the knowledge that it has diligently reported the error condition.

But if stderr has been closed, then the write will fail. At that point, your program may do something unpredictable. It may carry on and ignore the failure, or it may immediately exit, considering the execution environment so broken that it cannot safely continue. Or whatever else the programmer decided the program should do when its world has become a dystopian hell.

All programs are assured that stdin, stdout and stderr will exist and will be readable/writable in an appropriate and reasonable manner. By closing one of them, you have violated your promise to this program. This is not acceptable.

Of course, an even better solution would be to actually log the errors somewhere, so you can go back and read them and figure out what's wrong.