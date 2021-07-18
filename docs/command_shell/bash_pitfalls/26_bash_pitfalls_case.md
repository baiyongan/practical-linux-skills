# Bash Pitfalls Case 26
## echo "~"

Tilde expansion only applies when '~' is unquoted. In this example echo writes '~' to stdout, rather than the path of the user's home directory.

Quoting path parameters that are expressed relative to a user's home directory should be done using $HOME rather than '~'. For instance consider the situation where $HOME is "/home/my photos".

```shell
 "~/dir with spaces" # expands to "~/dir with spaces"
 ~"/dir with spaces" # expands to "~/dir with spaces"
 ~/"dir with spaces" # expands to "/home/my photos/dir with spaces"
 "$HOME/dir with spaces" # expands to "/home/my photos/dir with spaces"
 ```
 