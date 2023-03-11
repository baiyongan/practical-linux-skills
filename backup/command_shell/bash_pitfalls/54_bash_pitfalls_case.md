# Bash Pitfalls Case 54
## sudo ls /foo/*

This is very similar to the previous pitfall. Globbing is also done before the command is executed. If the directory isn't readable by your normal user privileges, then you may need the globbing to be done in a shell that has the sudo-granted privileges:

```shell
sudo sh -c 'ls /foo/*'
```