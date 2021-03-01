# Bash Pitfalls Case 08
## grep foo bar | while read -r; do ((count++)); done

The code above looks OK at first glance, doesn't it? Sure, it's just a poor implementation of grep -c, but it's intended as a simplistic example. Changes to count won't propagate outside the while loop because each command in a pipeline is executed in a separate SubShell. This surprises almost every Bash beginner at some point.

POSIX doesn't specify whether or not the last element of a pipeline is evaluated in a subshell. Some shells such as ksh93 and Bash >= 4.2 with shopt -s lastpipe enabled will run the while loop in this example in the original shell process, allowing any side-effects within to take effect. Therefore, portable scripts must be written in such a way as to not depend upon either behavior.

For workarounds for this and similar issues, please see Bash FAQ #24. It's a bit too long to fit here.


