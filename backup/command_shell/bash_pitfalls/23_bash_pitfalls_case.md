# Bash Pitfalls Case 23
## echo "Hello World!"

The problem here is that, in an interactive Bash shell (in versions prior to 4.3), you'll see an error like:

```shell
 bash: !": event not found
```

This is because, in the default settings for an interactive shell, Bash performs csh-style history expansion using the exclamation point. This is not a problem in shell scripts; only in interactive shells.

Unfortunately, the obvious attempt to "fix" this won't work:

```shell
 $ echo "hi\!"
 hi\!
```

The easiest solution is unsetting the histexpand option: this can be done with set +H or set +o histexpand

Question: Why is playing with histexpand more apropriate than single quotes?

I personally ran into this issue when I was manipulating song files, using commands like

```shell
mp3info -t "Don't Let It Show" ...
mp3info -t "Ah! Leah!" ...
```

Using single quotes is extremely inconvenient because of all the songs with apostrophes in their titles. Using double quotes ran into the history expansion issue. (And imagine a file that has both in its name. The quoting would be atrocious.) Since I never actually use history expansion, my personal preference was to turn it off in ~/.bashrc. -- GreyCat

These solutions will work:

```shell
 echo 'Hello World!'
```

or

```shell
 echo "Hello World"!
```

or

```shell
 set +H
 echo "Hello World!"
```

or

```shell
 histchars=
```

Many people simply choose to put set +H or set +o histexpand in their ~/.bashrc to deactivate history expansion permanently. This is a personal preference, though, and you should choose whatever works best for you.

Another solution is:

```shell
 exmark='!'
 echo "Hello, world$exmark"
```

In Bash 4.3 and newer, a double quote following ! no longer triggers history expansion, but history expansion is still performed within double quotes, so while echo "Hello World!" is OK, these will still be a problem:

```shell
 echo "Hello, World!(and the rest of the Universe)"
 echo "foo!'bar'"
```