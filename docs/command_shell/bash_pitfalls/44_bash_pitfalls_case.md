# Bash Pitfalls Case 44
## cmd; (( ! \$? )) || die

\$? is only required if you need to retrieve the exact status of the previous command. If you only need to test for success or failure (any non-zero status), just test the command directly. e.g.:

```shell
if cmd; then
    ...
fi
```

Checking an exit status against a list of alternatives might follow a pattern like this:

```shell
cmd
status=$?
case $status in
    0)
        echo success >&2
        ;;
    1)
        echo 'Must supply a parameter, exiting.' >&2
        exit 1
        ;;
    *)
        echo "Unknown error $status, exiting." >&2
        exit "$status"
esac
```