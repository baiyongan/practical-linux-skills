# Bash Pitfalls Case 49
## OIFS="\$IFS"; ...; IFS="\$OIFS"

Directly assigning a variable's value to a temporary variable isn't alone enough to restore its state. The assignment will always result in a set but empty temporary variable even if the initial variable was unset. This is a particular problem for IFS because an empty IFS has a completely different meaning from an unset IFS, and setting IFS to a temporary value for a command or two is a common requirement.

An easy workaround is to designate a prefix to distinguish set from unset vars, then strip it when finished.

```shell
oIFS=${IFS+_${IFS}}
IFS=/; echo "${array[*]}"
${oIFS:+'false'} unset -v IFS || IFS=${oIFS#_}
```

A local variable is usually preferable when possible.

```shell
f() {
  local IFS
  IFS=/; echo "${array[*]}"
}
f
```

Subshells are another possibility.

```shell
( IFS=/; echo "${array[*]}" )
```
