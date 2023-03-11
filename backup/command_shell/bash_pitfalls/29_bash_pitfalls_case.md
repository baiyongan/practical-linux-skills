# Bash Pitfalls Case 29
## sed 's/\$foo/good bye/'

In single quotes, bash parameter expansions like $foo do not get expanded. That is the purpose of single quotes, to protect characters like $ from the shell.

Change the quotes to double quotes:

```shell
 foo="hello"; sed "s/$foo/good bye/"
```

But keep in mind, if you use double quotes you might need to use more escapes. See the Quotes page.