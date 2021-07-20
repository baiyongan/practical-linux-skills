# Bash Pitfalls Case 38
## ed file <<<"g/d\{0,3\}/s//e/g" fails

The problem caused because ed doesn't accept 0 for \{0,3\}.

You can check that the following do work:

```shell
ed file <<<"g/d\{1,3\}/s//e/g"
```

Note that this happens even though POSIX states that BRE (which is the Regular Expression flavor used by ed) should accept 0 as the minimum number of occurrences (see section 5).