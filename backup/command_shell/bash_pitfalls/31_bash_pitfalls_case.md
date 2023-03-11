# Bash Pitfalls Case 31
## ps ax | grep gedit

The fundamental problem here is that the name of a running process is inherently unreliable. There could be more than one legitimate gedit process. There could be something else disguising itself as gedit (changing the reported name of an executed command is trivial). For real answers to this, see ProcessManagement.

The following is the quick and dirty stuff.

Searching for the PID of (for example) gedit, many people start with

```shell
$ ps ax | grep gedit
10530 ?        S      6:23 gedit
32118 pts/0    R+     0:00 grep gedit
```

which, depending on a RaceCondition, often yields grep itself as a result. To filter grep out:


```shell
ps ax | grep -v grep | grep gedit   # will work, but ugly
```

An alternative to this is to use:

```shell
ps ax | grep '[g]edit'              # quote to avoid shell GLOB
```

This will ignore the grep itself in the process table as that is [g]edit and grep is looking for gedit once evaluated.

On GNU/Linux, the parameter -C can be used instead to filter by commandname:

```shell
$ ps -C gedit
  PID TTY          TIME CMD
10530 ?        00:06:23 gedit
```

But why bother when you could just use pgrep instead?

```shell
$ pgrep gedit
10530
```

Now in a second step the PID is often extracted by awk or cut:

```shell
$ ps -C gedit | awk '{print $1}' | tail -n1
```

but even that can be handled by some of the trillions of parameters for ps:

```shell
$ ps -C gedit -opid=
10530
```

If you're stuck in 1992 and aren't using pgrep, you could use the ancient, obsolete, deprecated pidof (GNU/Linux only) instead:

```shell
$ pidof gedit
10530
```

and if you need the PID to kill the process, pkill might be interesting for you. Note however that, for example, pgrep/pkill ssh would also find processes named sshd, and you wouldn't want to kill those.

Unfortunately some programs aren't started with their name, for example firefox is often started as firefox-bin, which you would need to find out with - well - ps ax | grep firefox. :) Or, you can stick with pgrep by adding some parameters:

```shell
$ pgrep -fl firefox
3128 /usr/lib/firefox/firefox
7120 /usr/lib/firefox/plugin-container /usr/lib/flashplugin-installer/libflashplayer.so -greomni /usr/lib/firefox/omni.ja 3128 true plugin
```

Please read ProcessManagement. Seriously.


