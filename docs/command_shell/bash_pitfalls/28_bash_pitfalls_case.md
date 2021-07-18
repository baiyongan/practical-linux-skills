# Bash Pitfalls Case 28
## export foo=~/bar

Tilde expansion (with or without a username) is only guaranteed to occur when the tilde appears at the beginning of a word, either by itself or followed by a slash. It is also guaranteed to occur when the tilde appears immediately after the = in an assignment.

However, the export and local commands do not necessarily constitute an assignment. In some shells (like Bash), export foo=~/bar will undergo tilde expansion; in others (like dash), it will not.

```shell
 foo=~/bar; export foo    # Right!
 export foo="$HOME/bar"   # Right!
```

Using parameter expansion instead of tilde expansion lets use further improve it to:

.

```shell
 export foo="${HOME%/}/bar" # Better!
```

Which yields /bar instead of //bar when $HOME is / (which used to be common for the root user at least) which is better as on some systems, paths that start with a double-slash have a special meaning.