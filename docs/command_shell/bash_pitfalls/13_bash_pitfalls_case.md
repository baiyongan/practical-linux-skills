# Bash Pitfalls Case 13
## cat file | sed s/foo/bar/ > file

你无法在同一管道中读取文件并写入文件。 根据管道的作用，文件可能会被破坏（变成 0 字节，或者可能是等于操作系统管道缓冲区大小的字节数），或者它可能会增长，直到填满可用磁盘空间，又或达到你的操作系统的文件大小限制，或你的配额等。

如果你想安全地对文件进行更改，而不是追加到文件末尾，请使用文本编辑器。

```shell
 printf %s\\n ',s/foo/bar/g' w q | ed -s file
```

如果你正在做一些使用文本编辑器无法完成的事情，则必须在某个时候创建一个临时文件 (*)。 例如，以下是完全可移植的：

```shell
 sed 's/foo/bar/g' file > tmpfile && mv tmpfile file
```

以下内容仅适用于 GNU sed 4.x：

```shell
 sed -i 's/foo/bar/g' file(s)
```

请注意，这也会创建一个临时文件，并执行相同的重命名技巧——它只是透明地处理它。

以下等效命令需要 perl 5.x：

```shell
 perl -pi -e 's/foo/bar/g' file(s)
```

有关替换文件内容的更多详细信息，请参阅 [Bash FAQ #21](http://mywiki.wooledge.org/BashFAQ/021)。

> moreutils 的 sponge 在其手册中使用了这个例子：

```shell
 sed '...' file | grep '...' | sponge file
```

这个版本没有使用临时文件和 `mv` 的方式，而是在打开和写入文件之前 “soaks up”（手册中的实际描述！）所有数据。如果在写入操作期间程序或系统崩溃，此版本将导致数据丢失，因为此时磁盘上没有原始文件的副本。

在系统崩溃/断电的情况下，使用临时文件 + `mv` 的方式，仍然会存在轻微的数据丢失风险； 若要 100% 确定旧文件或新文件在断电后仍然存在，你必须在 `mv` 之前使用 `sync`。