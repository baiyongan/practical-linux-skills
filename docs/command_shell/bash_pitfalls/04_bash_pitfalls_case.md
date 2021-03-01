# Bash Pitfalls Case 04
## [ $foo = "bar" ]

This is very similar to the issue in pitfall #2, but I repeat it because it's so important. In the example above, the quotes are in the wrong place. You do not need to quote a string literal in bash (unless it contains metacharacters or pattern characters). But you should quote your variables if you aren't sure whether they could contain white space or wildcards.

This example can break for several reasons:

- If a variable referenced in [ doesn't exist, or is blank, then the [ command would end up looking like:

```shell
[ = "bar" ] # Wrong!
```

...and will throw the error: unary operator expected. (The = operator is binary, not unary, so the [ command is rather shocked to see it there.)

- If the variable contains internal whitespace, then it gets split into separate words before the [ command sees it. Thus:

```shell
[ multiple words here = "bar" ]
```

While that may look OK to you, it's a syntax error as far as [ is concerned. The correct way to write this is:

```shell
# POSIX
[ "$foo" = bar ] # Right!
```

This works fine on POSIX-conformant implementations even if $foo begins with a -, because POSIX [ determines its action depending on the number of arguments passed to it. Only very ancient shells have a problem with this, and you shouldn't worry about them when writing new code (see the x"$foo" workaround below).

In Bash and many other ksh-like shells, there is a superior alternative which uses the [[ keyword.

```shell
# Bash / Ksh
[[ $foo == bar ]] # Right!
```

You don't need to quote variable references on the left-hand side of = in [[ ]] because they don't undergo word splitting or globbing, and even blank variables will be handled correctly. On the other hand, quoting them won't hurt anything either. Unlike [ and test, you may also use the identical ==. Do note however that comparisons using [[ perform pattern matching against the string on the right hand side, not just a plain string comparison. To make the string on the right literal, you must quote it if any characters that have special meaning in pattern matching contexts are used.

```shell
# Bash / Ksh
match=b*r
[[ $foo == "$match" ]] # Good! Unquoted would also match against the pattern b*r.
```

You may have seen code like this:

```shell
# POSIX / Bourne
[ x"$foo" = xbar ] # Ok, but usually unnecessary.
```

The x"$foo" hack is required for code that must run on very ancient shells which lack [[, and have a more primitive [, which gets confused if $foo begins with a -. On said older systems, [ still doesn't care whether the token on the right hand side of the = begins with a -. It just uses it literally. It's just the left-hand side that needs extra caution.

Note that shells that require this workaround are not POSIX-conforming. Even the Heirloom Bourne shell doesn't require this (probably the non-POSIX Bourne shell clone that's still most widely in use as a system shell). Such extreme portability is rarely a requirement and makes your code less readable (and uglier).