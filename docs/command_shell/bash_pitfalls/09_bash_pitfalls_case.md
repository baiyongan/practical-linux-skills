# Bash Pitfalls Case 09
## if [grep foo myfile]

Many beginners have an incorrect intuition about if statements brought about by seeing the very common pattern of an if keyword followed immediately by a [ or [[. This convinces people that the [ is somehow part of the if statement's syntax, just like parentheses used in C's if statement.

This is not the case! if takes a command. [ is a command, not a syntax marker for the if statement. It's equivalent to the test command, except that the final argument must be a ]. For example:

```shell
# POSIX
if [ false ]; then echo "HELP"; fi
if test false; then echo "HELP"; fi
```

are equivalent -- both checking that the argument "false" is non-empty. In both cases HELP will always be printed, to the surprise of programmers from other languages guessing about shell syntax.

The syntax of an if statement is:

```shell
if COMMANDS
then <COMMANDS>
elif <COMMANDS> # optional
then <COMMANDS>
else <COMMANDS> # optional
fi # required
```

Once again, [ is a command. It takes arguments like any other regular simple command. if is a compound command which contains other commands -- and there is no [ in its syntax!

While bash has a builtin command [ and thus knows about [ it has nothing special to do with ]. Bash only passes ] as argument to the [ command, which requires ] to be the last argument only to make scripts look better.

There may be zero or more optional elif sections, and one optional else section.

The if compound command is made up of two or more sections containing lists of commands, each delimited by a then, elif, or else keyword, and is terminated by the fi keyword. The exit status of the final command of the first section and each subsequent elif section determines whether each corresponding then section is evaluated. Another elif is evaluated until one of the then sections is executed. If no then section is evaluated, then the else branch is taken, or if no else is given, the if block is complete and the overall if command returns 0 (true).

If you want to make a decision based on the output of a grep command, you do not want to enclose it in parentheses, brackets, backticks, or any other syntax! Just use grep as the COMMANDS after the if, like this:

```shell
if grep -q fooregex myfile; then
...
fi
```

If the grep matches a line from myfile, then the exit code will be 0 (true), and the then part will be executed. Otherwise, if there are no matches, grep will return non-zero and the overall if command will be zero.

See also:

[BashGuide/TestsAndConditionals](http://mywiki.wooledge.org/BashGuide/TestsAndConditionals)

[http://wiki.bash-hackers.org/syntax/ccmd/if_clause](http://wiki.bash-hackers.org/syntax/ccmd/if_clause)


