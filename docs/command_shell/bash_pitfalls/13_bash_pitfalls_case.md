# Bash Pitfalls Case 13
## cat file | sed s/foo/bar/ > file

You cannot read from a file and write to it in the same pipeline. Depending on what your pipeline does, the file may be clobbered (to 0 bytes, or possibly to a number of bytes equal to the size of your operating system's pipeline buffer), or it may grow until it fills the available disk space, or reaches your operating system's file size limitation, or your quota, etc.

If you want to make a change to a file safely, other than appending to the end of it, use a text editor.

```shell
 printf %s\\n ',s/foo/bar/g' w q | ed -s file
```

If you are doing something that cannot be done with a text editor there must be a temporary file created at some point(*). For example, the following is completely portable:

```shell
 sed 's/foo/bar/g' file > tmpfile && mv tmpfile file
```

The following will only work on GNU sed 4.x:

```shell
 sed -i 's/foo/bar/g' file(s)
```

Note that this also creates a temporary file, and does the same sort of renaming trickery -- it just handles it transparently.

And the following equivalent command requires perl 5.x:

```shell
 perl -pi -e 's/foo/bar/g' file(s)
```

For more details on replacing contents of files, please see Bash FAQ #21.

(*) sponge from moreutils uses this example in its manual:

```shell
 sed '...' file | grep '...' | sponge file
```

Rather than using a temporary file plus an atomic mv, this version "soaks up" (the actual description in the manual!) all the data, before opening and writing to the file. This version will cause data loss if the program or system crashes during the write operation, because there's no copy of the original file on disk at that point.

Using a temporary file + mv still incurs a slight risk of data loss in case of a system crash / power loss; to be 100% certain that either the old or the new file will survive a power loss, you must use sync before the mv.