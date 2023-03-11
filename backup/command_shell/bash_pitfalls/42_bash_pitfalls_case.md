# Bash Pitfalls Case 42
## for file in ./* ; do if [[ \$file != *.* ]]

One way to prevent programs from interpreting filenames passed to them as options is to use pathnames (see pitfall #3 above). For files under the current directory, names may be prefixed with a relative pathname ./.

In the case of a pattern like *.* however, problems can arise because it matches a string of the form ./filename. In a simple case, you can just use the glob directly to generate the desired matches. If however a separate pattern-matching step is required (e.g. the results have been preprocessed and stored in an array, and need to be filtered), it could be solved by taking the prefix into account in the pattern: [[ $file != ./*.* ]], or by stripping the pattern from the match.

```shell
# Bash
shopt -s nullglob
for path in ./*; do
    [[ ${path##*/} != *.* ]] && rm "$path"
done

# Or even better
for file in *; do
    [[ $file != *.* ]] && rm "./$file"
done

# Or better still
for file in *.*; do
    rm "./$file"
done
```

Another possibility is to signal the end of options with a -- argument. (Again, covered in #pf3).

```shell
shopt -s nullglob
for file in *; do
    [[ $file != *.* ]] && rm -- "$file"
done
```