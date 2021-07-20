# Bash Pitfalls Case 48
## export CDPATH=.:~/myProject

Do not export CDPATH.

Setting CDPATH in .bashrc is not an issue, but exporting it will cause any bash or sh script you run, that happen to use cd, to potentially change behaviour.

There are two problems. A script that does the following:

```shell
cd some/dir || exit
cmd to be run in some/dir
```

may change directory to ~/myProject/some/dir instead of ./some/dir, depending on what directories exist at the time. So the cd may succeed and take the script to the wrong directory, with potentially harmful effects of the following commands which now run in a different directory than intended.

The second problem is when cd is run in a context where the output is captured:

```shell
output=$(cd some/dir && some command)
```

As a side-effect when CDPATH is set, cd will output something like /home/user/some/dir to stdout to indicate that it found a directory through CDPATH, which in turn will end up in the output variable along with the intended output of some command.

A script can make itself immune to a CDPATH inherited from the environment by always prepending ./ to relative paths, or run unset CDPATH at the start of the script, but don't assume every scripter has taken this pitfall into account, so don't export CDPATH.