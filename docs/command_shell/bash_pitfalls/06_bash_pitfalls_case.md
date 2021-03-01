# Bash Pitfalls Case 06
## [ "$foo" = bar && "$bar" = foo ]

You can't use && inside the old test (or [) command. The Bash parser sees && outside of [[ ]] or (( )) and breaks your command into two commands, before and after the &&. Use one of these instead:

```shell
[ bar = "$foo" ] && [ foo = "$bar" ] # Right! (POSIX)
[[ $foo = bar && $bar = foo ]]       # Also right! (Bash / Ksh)
```

(Note that we reversed the constant and the variable inside [ for the legacy reasons discussed in pitfall #4. We could also have reversed the [[ case, but the expansions would require quoting to prevent interpretation as a pattern.) The same thing applies to ||. Either use [[ instead, or use two [ commands.

Avoid this:

```shell
[ bar = "$foo" -a foo = "$bar" ] # Not portable.
```

The binary -a and -o, and ( / ) (grouping) operators are XSI extensions to the POSIX standard. All are marked as obsolescent in POSIX-2008. They should not be used in new code. One of the practical problems with [ A = B -a C = D ] (or -o) is that POSIX does not specify the results of a test or [ command with more than 4 arguments. It probably works in most shells, but you can't count on it. If you have to write for POSIX shells, then you should use two test or [ commands separated by a && operator instead.