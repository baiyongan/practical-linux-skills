# Bash Pitfalls Case 53
## sudo mycmd > /myfile

Redirection is done before the command is executed. Usually that doesn't matter, but with sudo we have a command being executed as a different user than the redirection.

If the redirection must be executed with sudo-granted privileges, then you need a wrapper:

```shell
sudo sh -c 'mycmd > /myfile'
```

Instead of a wrapper you can use tee:

```shell
mycmd | sudo tee /myfile >/dev/null
```

This may be easier to write if mycmd has a lot of quoting.