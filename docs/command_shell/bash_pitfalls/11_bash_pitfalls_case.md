# Bash Pitfalls Case 11
## if [ [ a = b ] && [ c = d ] ]; then ...

Here we go again. [ is a command. It is not a syntactic marker that sits between if and some sort of C-like "condition". Nor is it used for grouping. You cannot take C-like if commands and translate them into Bash commands just by replacing parentheses with square brackets!

If you want to express a compound conditional, do this:

```shell
if [ a = b ] && [ c = d ]; then ...
```

Note that here we have two commands after the if, joined by an && (logical AND, shortcut evaluation) operator. It's precisely the same as:

```shell
if test a = b && test c = d; then ...
```

If the first test command returns false, the body of the if statement is not entered. If it returns true, then the second test command is run; and if that also one returns true, then the body of the if statement will be entered. (C programmers are already familiar with &&. Bash uses the same short-circuit evaluation. Likewise || does short-circuit evaluation for the OR operation.)

The [[ keyword does permit the use of &&, so it could also be written this way:

```shell
if [[ a = b && c = d ]]; then ...
```

See pitfall #6 for a pitfall related to tests combined with conditional operators.


