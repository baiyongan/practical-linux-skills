# 7 Encoding pathnames
It is possible to encode pathnames so that all pathnames can be handled. There is no standard POSIX mechanism for doing this encoding, unfortunately.

encodef is a small utility I wrote that can encode and decode filenames in a few formats. With it, you can do this:

```shell
 # This version is POSIX portable; in practice
 # you can often use "-print0" instead of "-exec printf '%s\0' {} \;"
 for encoded_pathname in $(find . -exec printf '%s\0' {} \; | encodef ) ; do
   file="$(encodef -d -Y -- "$encoded_pathname")" ; file="${file%Y}"
   COMMAND "$file" # Use quoted "$file", not $file, everywhere.
 done
```
