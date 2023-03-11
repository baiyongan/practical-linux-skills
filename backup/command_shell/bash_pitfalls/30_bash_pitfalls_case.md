# Bash Pitfalls Case 30
## tr [A-Z] [a-z]

There are (at least) three things wrong here. The first problem is that [A-Z] and [a-z] are seen as globs by the shell. If you don't have any single-lettered filenames in your current directory, it'll seem like the command is correct; but if you do, things will go wrong. Probably at 0300 hours on a weekend.

The second problem is that this is not really the correct notation for tr. What this actually does is translate '[' into '['; anything in the range A-Z into a-z; and ']' into ']'. So you don't even need those brackets, and the first problem goes away.

The third problem is that depending on the locale, A-Z or a-z may not give you the 26 ASCII characters you were expecting. In fact, in some locales z is in the middle of the alphabet! The solution to this depends on what you want to happen:

```shell
 # Use this if you want to change the case of the 26 latin letters
 LC_COLLATE=C tr A-Z a-z

 # Use this if you want the case conversion to depend upon the locale, which might be more like what a user is expecting
 tr '[:upper:]' '[:lower:]'
```

The quotes are required on the second command, to avoid globbing.