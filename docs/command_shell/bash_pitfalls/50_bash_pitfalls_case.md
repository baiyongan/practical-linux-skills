# Bash Pitfalls Case 50
## hosts=( \$(aws ...) )

It is not safe to populate an array with a raw $(...) CommandSubstitution. The output of the command undergoes word splitting (on all whitespace, even ones that are inside quotes) and then globbing. If there's a word like * or eh? or [abc] in the result, it will be expanded based on filenames in the current working directory.

To select a replacement, you need to know whether the command writes its output on a single line, or multiple lines. If it's a single line:

```shell
read -ra hosts < <(aws ...)
```

If it's multiple lines (and you're targeting bash 4.0 or later):

```shell
readarray -t hosts < <(aws ...)
```

If it's multiple lines (and you want compatibility with bash 3.x, or want your command's exit status to be reflected in success or failure of the read operation without depending on behavior only available in bash 4.4 and newer):

```shell
IFS=$'\n' read -r -d '' -a hosts < <(aws ... && printf '\0')
```

This will prevent globbing. It still won't help you if you needed to avoid splitting on quoted whitespace, but unfortunately nothing bash can do handles that case. For generalized CSV (comma-separated value) file handling, you really need to switch to a language that has a dedicated CSV input library.