# 3 基本规则的基本原理

## 3.1 双引号参数(变量)引用和命令替换

As described by any Bourne shell programming book, always use double-quotes (") to surround variable references and command substitutions, unless you are certain they can only produce alphanumeric characters or you have specially prepared things. The dangerous characters are whitespace or shell pathname expansion (glob) characters like "*", because unquoted variable references and command substitutions undergo shell field splitting and pathname expansion:

- Field splitting splits a word into multiple words; by default they are split by space, tab, or newline. This expansion can be controlled or disabled by setting the variable IFS, but you have to specially set it.
- Pathname expansion looks for filename patterns, and if they are found, splits up a word into multiple words for each filename. If the unquoted variable reference or command substitution produces a character like *, shell will normally try to replace that with a list of the filenames that match the pattern. This expansion can be disabled using "set -f".
The good news is that once you get into the habit, this is an easy style rule to follow. Even if you know that they can only produce characters that will not cause problems, quoting is a good idea, since the script might change in the future. It is easy to remember "alphanumeric characters okay" than a more complicated rule, and if you allow more than alphanumeric characters, it is likely that the variable will eventually allow dangerous characters. Lots of scripts already follow this rule, so while it’s annoying, it’s not too bad. Here are some examples:

Don’t use	Instead use
$file	"$file"
$(pwd)	"$(pwd)"
$(dirname $file)	"$(dirname "$file")"

By the way, it turns out that the POSIX spec is unclear whether or not field splitting applies to arithmetic expansion in shell; most (but not all) implementations do apply field splitting in this case.

## 3.2 在每个脚本的开头将 IFS 设置为仅换行符和制表符

One of the first non-comment commands in every shell script should be:

```shell
   IFS="$(printf '\n\t')"
   # or:
   IFS="`printf '\n\t'`"
   # Widely supported, POSIX added http://austingroupbugs.net/view.php?id=249
   IFS=$'\n\t'
```

To understand this recommendation, you need to know what IFS is. IFS is the list of input field separators in shell. The POSIX specification XCU section 2.6.5 explains that after various expansions (such as parameter expansion and command subsitution), the results not in double-quotes are split up where they include input field separators. By default IFS is space, tab, and newline.

This recommendation sets the IFS variable so that the "space" character is no longer an input field separator, and thus only newline and tab are field separators. If you need to run on really old systems the second form with backquotes is better, but the first one is easier to read, POSIX compliant, and very portable - it works on any system not in a museum.

This doesn’t help security very much, but it does help reliability. If you make a mistake in your script, and the script encounters a pathname (or other data) with a space, your script is more likely to work correctly. Filenames with tabs and newlines are almost never used except by attackers, but users often use spaces; doing this will prevent file splitting from unintentionally splitting up filenames with spaces. So if you forget to surround a variable reference with double-quotes, or use a for loop with a simple command substitution, it is less likely to fail. It also makes it possible to combine options and filenames with spaces; you can use filenames with spaces (as usual), and separate the options with tabs (this isn’t a common convention, but I think it’s a reasonable one). It is also really easy to do this; just add one line near the top.

The recommended IFS command sets newline and then tab. It is harder to do it in the other order in some shells, because $(...) consumes trailing newlines. The easy way is to use IFS=$'\t\n', which is widely supported but has only recently been added to POSIX.

You can still build a list of command options inside a single shell variable, even when space isn’t in IFS. You just need to use tab or newline to separate parameters, and not space. You can even embed pathnames with spaces in this variable, since spaces are no longer field separators.

You might also want to put "set -eu" or at least "set -u" at the beginning of your scripts, along with setting IFS. This does nothing for pathnames, but these can help detect other script errors.

By the way, the need for proper quoting is not limited to Bourne shells. The Windows shell also requires proper quoting, and improper quoting can lead to vulnerabilities. A user merely needs to create filenames with characters such as ampersands, and an improperly-quoted shell program might end up running it. For example, imagine if an attacker can create a directory of the form "name&command_to_execute", say on a fileserver. Then a Windows script which fails to quote properly (e.g., it has ECHO %CD% or SET CurrentPath=%CD% without putting double-quotes around %CD%) would end up running the command of the attacker’s choosing.

## 3.3 前缀所有 glob，以便它们无法扩展以"-"开头

A "glob" is a pattern for pathname matching like "*.pdf". Whenever you use globbing to select files, never begin with a globbing character (typically the characters "*", "?", or "["). If you’re starting from the current directory, prefix the glob with "./" like this:

```shell
 cat ./*                   # Use this, NOT "cat *" ... Must have 1+ files.
 for file in ./* ; do      # Use this, NOT "for file in *" (beware empty lists)
   ...
 done
```

This is important because almost all commands will interpret a string beginning with dash as an option, not as a filename, until they see something that does not begin with dash. Globs are expanded by the shell into a list of filenames, and dash is earlier in the sort order compared to before alphanumerics, so it is easy for attackers to make this happen.

If you always prefix pathnames (e.g., those acquired through globs), then pathnames starting with "-" will always be handled correctly. Globbing is often the easiest way to handle all files, or a subset of them, in a specific directory, but you need to make sure you do it correctly.

## 3.4 接受路径名时，请检查路径名是否以"-"开头，然后是否以" ./"开头

Similar to the previous rule, if you read in a pathname, as early as possible see if it begins with "-"... if it does, prepend "./". This eliminates this source of pathnames that are confused as option flags.

## 3.5 在显示或存储路径名时要小心

Filter or encode pathnames before displaying them. The biggest problem is that pathnames could contain control characters that control the terminal and/or the GUI display, causing nasty side-effects on display. Displaying pathnames can even cause a security vulnerability in some situations (!). If you must display pathnames, consider encoding or stripping out control characters first (many ls implementations do this when the output is a terminal). You can strip out the control characters this way:

```shell
 printf '%s' "$file" | LC_ALL=POSIX tr -d '[:cntrl:]'
```

In addition, you have no way of knowing for certain what the pathname’s character encoding is, so if you got a pathname from someone else, and they do not use UTF-8 (including ASCII), you’re likely to end up with garbage mojibake.

In practice, what most people do is exchange pathnames and hope that they are UTF-8. If you both use the same locale, you could use that instead, but UTF-8 is the only encoding in wide use for Unix pathnames that can handle arbitrary languages. Most modern GUI toolkits presume that filenames are UTF-8, even though nothing actually ensures that this is true. If you must display pathnames, consider forcing them to display as UTF-8. I encourage you to always encode pathnames in UTF-8... but beware that nothing actually enforces this common convention. Thus, you will want to enforce it yourself where you can.

One way you can avoid displaying non-UTF-8 filenames in shell is to try to convert them to UTF-8 using iconv. The iconv program is in POSIX, and it can strip out characters not in a given encoding. Sadly, the encodings that must be supported by iconv are not standardized. Still, GNU iconv supports UTF-8, and other systems are likely to do so, so this will probably work:

```shell
  printf '%s' "$file" | iconv -cs -f UTF-8 -t UTF-8
```

A common approach for storing pathnames in files, or to transmit them in data formats, is to separate them with newlines and/or tabs. Sadly, this does not work in the general case, since pathnames can include both characters. You need to forbid such nasty filenames, escape them, or use \0 to separate the pathnames. If you can forbid them, that is the easiest... but you may not have that option.

## 3.6 不要依赖"--"

Many books, and the POSIX standard, mistakenly advocate using "--" between the options and pathnames as the primary method to deal with filenames beginning with "-". This is impractical and bad advice:

1. For "--" to work, all maintainers would have to faithfully use "--" in practically every command invocation. That just doesn’t happen in real life, even after decades of people trying. People forget it all the time; no one is that consistent, especially since code seems to work without it. Very few commands require it, after all.
2. You can’t do it anyway, even if you were perfectly consistent; many programs and commands do not support "--". POSIX even explicitly forbids echo from supporting "--", and echo must support "-n" (and GNU coreutils echo supports other options too).
Thus, as a practical matter you need to do something else; by always prefixing filenames if they start with dash, as recommended earlier, the problem disappears.

Do feel free to use "--" between options and pathnames, when you can do it, as an additional protective measure. But using "--" as your primary (or only) mechanism for dash-prefixed filenames is bad idea. You are better off prefixing the pathnames when you get the pathname, since then you only have to do it once per pathname. Once you prefix the pathname it doesn’t matter if you remember "--" or not; it just works correctly.

## 3.7 使用通配符并适当查找(并处理空匹配项)

There are two major ways to get sets of pathnames in the shell, glob patterns and the find command. Globs are primarily useful for a short list of unhidden filenames in one directory; find is useful for other situations, including recursively descending into subdirectories.

In both cases you have to worry around what happens when there are zero matches. If you just gave "command" and something that gave a list of filenames, most commands will hang while trying to read from standard input. The easy solution in this case is to add "/dev/null" to the end... assuming you can do that.

The next two sections examine glob patterns and the find command in turn.
