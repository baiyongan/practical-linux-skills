# Bash Pitfalls Case 58
## month=\$(date +%m); day=\$(date +%d)

Calling date multiple times is a bad idea. Imagine what happens if the first call occurs a millisecond before midnight on April 30, and the second call occurs a millisecond after midnight on May 1. You would end up with month=04 and day=01.

It's better to call date one time, retrieving all of the fields you want in a single invocation.

A common idiom for that:

```shell
eval "$(date +'month=%m day=%d year=%Y dayname="%A" monthname="%B"')"
```

Or with bash's (4.2 or above) printf builtin:

```shell
printf -v d '%(month=%m day=%d year=%Y dayname="%A" monthname="%B")T'
eval "$d"
```

Remember things like month or day names are locale-dependent, hence the quotes around %A or %B to avoid problems in locales where day or month names contain spaces or other special characters for the shell.

Or, you may retrieve a timestamp in epoch format (seconds since the start of 1970), and then use that to generate human-readable date/time fields as needed.

```shell
# Requires bash 4.2 or above
printf -v now '%(%s)T' -1        # Or now=$EPOCHSECONDS in bash 5.0
                                 # -1 may be omitted in 4.3 or above
printf -v month '%(%m)T' "$now"
printf -v day '%(%d)T' "$now"
```

If your system's strftime() doesn't support %s, you can get the epoch time with:

```shell
now=$(awk 'BEGIN{srand(); print srand()}')
```