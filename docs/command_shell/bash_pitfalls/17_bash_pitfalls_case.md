# Bash Pitfalls Case 17
## echo <<EOF

A here document is a useful tool for embedding large blocks of textual data in a script. It causes a redirection of the lines of text in the script to the standard input of a command. Unfortunately, echo is not a command which reads from stdin.

```shell
  # This is wrong:
  echo <<EOF
  Hello world
  How's it going?
  EOF

  # This is what you were trying to do:
  cat <<EOF
  Hello world
  How's it going?
  EOF

  # Or, use quotes which can span multiple lines (efficient, echo is built-in):
  echo "Hello world
  How's it going?"
```

Using quotes like that is fine -- it works great, in all shells -- but it doesn't let you just drop a block of lines into the script. There's syntactic markup on the first and last line. If you want to have your lines untouched by shell syntax, and don't want to spawn a cat command, here's another alternative:

```shell
  # Or use printf (also efficient, printf is built-in):
  printf %s "\
  Hello world
  How's it going?
  "
```

In the printf example, the \ on the first line prevents an extra newline at the beginning of the text block. There's a literal newline at the end (because the final quote is on a new line). The lack of \n in the printf format argument prevents printf adding an extra newline at the end. The \ trick won't work in single quotes. If you need/want single quotes around the block of text, you have two choices, both of which necessitate shell syntax "contaminating" your data:

```shell
  printf %s \
  'Hello world
  '

  printf %s 'Hello world
  '
```
