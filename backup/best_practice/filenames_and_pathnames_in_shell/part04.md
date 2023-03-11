# 4 Globbing
Globbing is a simple language specifically designed for filename handling, primarily to create lists of unhidden files in a particular directory. In this language, "*" matches all non-hidden files in the current directory, "*.pdf" matches all non-hidden files in the current directory ending in ".pdf" - and so on.

The good news about globbing in shell is that glob expansion is built into the shell and done after field (IFS) expansion. Thus, as long as you directly use globs as command parameters or as part of a "for" loop, you will have no problem with pathnames containing whitespace or control characters (since they will not undergo field expansion). There is also no challenge getting the information back into shell; the shell is doing the processing.

However, if a pathname begins with "-", glob will dutifully expand it, confusing any command later. As noted above, the recommended solution is to always prefix a glob with something that does not begin with dash, such as "./".

Remember that globbing normally skips hidden files (those beginning with "."). Often that is what you want. If you want the hidden files in a directory instead, you may want to use "find" instead. You can get the hidden files with a glob by adding two more globbing patterns:

```shell
 .[!.]* ..?*
```

In many cases even a simple glob could fail to match, and adding globbing patterns to find hidden files makes this even more likely... which leads us to the problem of handling empty pathname lists.

## 4.1 如果可能存在文件名的空列表时，请留意 globs
Beware of globbing if there might be no matches with the pattern (and this is often the case). By default, if a glob like ./*.pdf matches no files, then the original glob pattern will be returned instead.

This is almost never what you want. E.g., in a "for" loop this will cause the loop to execute once, but with the pattern instead of a pathname! Similarly, if you use a glob on a command line, such as cat ./*pdf, the result will be a request to open a non-existent file... which is almost never what you want.

You can use use globbing in a for loop, even if it might not match anything, using one of two approaches. One approach, which is completely portable, is to re-test for the existance of the file before using it in the loop:

```shell
 for file in ./* ; do        # Use this, NOT "for file in *"
   if [ -e "$file" ] ; then  # Make sure it exists and isn't an empty match
     COMMAND ... "$file" ...
   fi
 done
```

This is both ugly and a little inefficient (you have to re-test each file again). There are also pathological cases where the pattern doesn’t match but there is a file that is identical to the unmatched pattern (though for typical patterns that can’t happen), so you have to check your pattern to see if that could happen.

A more efficient but nonstandard solution for empty matches is to use a nonstandard shell extension called "null globbing". Null globbing fixes this by replacing an unmatched pattern with nothing at all. In bash you can enable nullglob with "shopt -s nullglob". In zsh, you can use setopt NULL_GLOB for the same result. Then this will work correctly:

```shell
 shopt -s nullglob  # Bash extension, so that empty glob matches will work
 for file in ./* ; do        # Use this, NOT "for file in *"
     COMMAND ... "$file" ...
 done
```

Null globbing can work well on the command line too, but there’s a catch. If all patterns might be empty, you have to include at least one file (such as /dev/null) that is okay to include, or it needs to be okay to run the command without any pathname arguments. Thus, you can use "cat ./*.pdf /dev/null".

Another problem with globbing is that if the list of matches is too long, on some older shells it will also fail. In short, in robust scripts, globbing should normally be used only as a "for" loop’s list.

## 4.2 globalstar 扩展
Traditional globbing is only useful when you want to process files in a particular directory. Some shells have added a nonstandard "globstar" extension, but it’s both nonstandard and has various limitations. I discuss it here, but you probably want to use find (discussed next).

With the globstar extension, the pattern "**" returns every pathname (including directories) in the current directory, recursively; it omits dot files, doesn’t descend into dot dirs, and sorts the file list.

Bash version 4 recently added this, but you must enable it with "shopt -s globstar". The zsh shell originally came up with this, and ksh93 was the first to copy it (but in ksh you have to enable it with "set -G"). Note that there’s no standard way to invoke it!

If you use this in a for loop list and combine it with nullglob, you can handle absolutely all pathnames easily and efficiently, including the empty case. That sounds great, but watch the fine print... I think there are many reasons to avoid this right now. It’s nonstandard, and gives you little control over the recursion. Most importantly, at least some implementations have trouble if there are links in the directories. Bash 4, at least, can get stuck in infinite loops if there are links. In many cases, find is currently the better approach for reliably doing recursive descent into directories.
