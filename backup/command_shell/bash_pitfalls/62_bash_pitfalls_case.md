# Bash Pitfalls Case 62
## (( hash[\$key]++ ))

Surprise! Associative arrays are even more broken in math contexts. The single-quoting trick from the previous pitfall isn't enough to fix this, either.

```shell
$ declare -A hash
$ key=\'\]
$ hash[$key]=17
$ (( hash[$key]++ ))
bash: ((: hash[']]++ : bad array subscript (error token is "hash[']]++ ")
$ (( 'hash[$key]++' ))
bash: ((: 'hash[']]++' : syntax error: operand expected (error token is "'hash[']]++' ")
```

According to Chet Ramey, the single quotes don't work here because the contents of a math context are treated as though they are double-quoted, and therefore the single quotes have no special meaning. However, backslash still has special meaning, and may be used to quote the index:

```shell
(( hash[\$key]++ ))    # Safe
```

Another safe way to modify an element of an associative array in a calculation is to make a temporary copy of the value in a regular (string) variable.

``shell
tmp=${hash[$key]}
((tmp++))              # Safe
hash[$key]=$tmp
```

Using an associative array element's value as an input in a calculation seems to be safe as long as you use the full ${hash[$key]} expansion syntax, rather than the shortened hash[$key] form.

```shell
$ x=$(( hash[$key] + 4 ))
bash: hash[']] + 4 : bad array subscript (error token is "hash[']] + 4 ")
$ x=$(( 'hash[$key]' + 4 ))
bash: 'hash[']]' + 4 : syntax error: operand expected (error token is "'hash[']]' + 4 ")
$ x=$(( ${hash[$key]} + 4 ))
$ 
```

Although of course this is subject to the same caveats as using any other parameter expansion (e.g. $i) in a math context. See Pitfall 45 for details.