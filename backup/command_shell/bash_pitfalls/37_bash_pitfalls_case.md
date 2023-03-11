# Bash Pitfalls Case 37
## [[ -e "\$broken_symlink" ]] returns 1 even though \$broken_symlink exists

Test follows symlinks, therefore if a symlink is broken, i.e. it points to a file that doesn't exists or is in a directory you don't have access to, test -e returns 1 for it even though it exists.

In order to work around it (and prepare against it) you should use:

```shell
# bash/ksh/zsh
[[ -e "$broken_symlink" || -L "$broken_symlink" ]]

# POSIX sh+test
[ -e "$broken_symlink" ] || [ -L "$broken_symlink" ]
```