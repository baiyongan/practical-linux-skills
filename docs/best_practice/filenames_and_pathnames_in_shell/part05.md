# 5 Find
If you want to process files beyond what normal globbing can do (e.g., recursively handle directories), or you don’t like the limitations on having to re-check for non-matches, use find. Re-implementing accurate directory traversal in the shell is possible, but both painful and silly; you would have to deal with symbolic links, hard links, renames during traversal, and other problems. The find tool is designed to handle this job; let it do its job. Sometimes you want to retrieve pathnames from programs other than find; in those cases the issues tend to be very similar.

An advantage of find is that it has lots of options for controlling how you process files and directories. You can use options to limit it to one directory, determine the ordering, and so on. It normally processes all files (including hidden ones), but you can use this pattern to skip hidden files (omit the "?" to skip directory "."):

```shell
 find . -name '.?*' -prune -o ....
```

The find command is always passed a starting directory, and it always returns values beginning with that directory. Thus, as long as the starting directory doesn’t begin with "-" you won’t have a problem with leading "-".

If you can directly use the find "-exec" option to run the command you want to use with the file, that is the easy way. Sadly, this is awkward to do in many cases, so you often want to get pathames from find back into the shell.

A challenge with find (and any other external program) is that it is more complicated to portably get information back into the shell. Pathnames can embed newlines; that means that reading filenames line-by-line fails (e.g, by read), splitting using IFS and newline cannot work directly, and command command substitution $(...) is awkward because it strips away trailing newlines. There are additional problems with Cygwin; Cygwin sometimes silently maps ending \r\n into \n, but it is legal to have filenames that differ only because one ends in \r\n and the other ends in \n; As a result, some constructs for handling arbitrary filenames do not work on Cygwin. Pathnames can also embed tabs and spaces, which by default causes unwanted field splitting. The usual approach is to separate pathnames with \0, but the mechanisms to handle this are not in the POSIX standard (e.g., options for "read" or "xargs" to handle such things).

Some of these approaches use "read", which is tricky to use. You typically need to use the "-r" option (so backslash is considered part of the line, and not an escape mechanism; otherwise filenames containing backslashes will cause problems). You also typically have to set IFS to be empty for the read command; otherwise, a pathname that includes IFS characters at the end of a filename would be corrupted (see the POSIX.1-2008 specification lines 103920-103925). If you want to read pathnames separated by \0 you’ll need another option, the discussion on null-separated pathnames gives more detail.

You’d like to use simple "for" loops, but by default if there is a pathname (glob) expansion character like "*" in a pathname, find will return the "*". If the shell receives a glob expansion character from a command substitution, by default the character will be re-expanded by the shell. You cannot just quote that expansion, either; that would make it appear that the list is just one pathname. Thus, in many cases you need to use "set -f" to disable expansion of filenames.

You could combine xargs with find using a pipe, and use newlines to separate pathnames, but don’t do it. The problem is that xargs interprets many characters in surprising ways, so it’s hard to use xargs correctly when using newlines as separators. The correct portable way to use xargs with newline separators requires that you pipe pathnames through another command like sed to do character substitutions. The result is complicated, hard to read, rediculously inefficient, and isn’t better than many other alternatives (e.g., it doesn’t handle newlines either); here it is:

```shell
 find . | sed -e 's/[^A-Za-z0-9]/\\&/g' | xargs -E "" COMMAND # DO NOT DO
```
