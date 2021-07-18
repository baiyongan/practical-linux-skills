# Bash Pitfalls Case 22
## cmd1 && cmd2 || cmd3

Some people try to use && and || as a shortcut syntax for if ... then ... else ... fi, perhaps because they think they are being clever. For instance,

```shell
 # WRONG!
 [[ -s $errorlog ]] && echo "Uh oh, there were some errors." || echo "Successful."
```

However, this construct is not completely equivalent to if ... fi in the general case. The command that comes after the && also generates an exit status, and if that exit status isn't "true" (0), then the command that comes after the || will also be invoked. For example:

```shell
 i=0
 true && ((i++)) || ((i--))  # WRONG!
 echo "$i"                   # Prints 0
```

What happened here? It looks like i should be 1, but it ends up 0. Why? Because both the i++ and the i-- were executed. The ((i++)) command has an exit status, and that exit status is derived from a C-like evaluation of the expression inside the parentheses. That expression's value happens to be 0 (the initial value of i), and in C, an expression with an integer value of 0 is considered false. So ((i++)) (when i is 0) has an exit status of 1 (false), and therefore the ((i--)) command is executed as well.

Another clever person thinks that we can fix it by using the pre-increment operator, since the exit status from ++i (with i initially 0) is true:

```shell
 i=0
 true && (( ++i )) || (( --i ))  # STILL WRONG!
 echo "$i"                       # Prints 1 by dumb luck
```

But that's missing the point of the example. It just happens to work by coincidence, and you cannot rely on x && y || z if y has any chance of failure! (This example still fails if we initialize i to -1 instead of 0.)

If you need safety, or if you simply aren't sure how this works, or if anything in the preceding paragraphs wasn't completely clear, please just use the simple if ... fi syntax in your programs.

```shell
 i=0
 if true; then
   ((i++))
 else
   ((i--))
 fi
 echo "$i"    # Prints 1
```

This section also applies to Bourne shell, here is the code that illustrates it:

```shell
 # WRONG!
 true && { echo true; false; } || { echo false; true; }
```

Output is two lines "true" and "false", instead the single line "true".