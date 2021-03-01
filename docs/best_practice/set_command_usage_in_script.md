# 设置 'set -euxo pipefail'，使脚本更安全[翻译]

开发人员通常会写bash脚本，就像用高级语言编写代码一样。 这是一个很大的错误，因为高级语言提供了默认情况下，在bash脚本中并不存在的防护措施。 例如，当尝试从未初始化的变量读取时，Ruby脚本将引发错误，而bash脚本则不会。 在本文中，我们将探讨如何对此进行改进。

bash shell 附带了几个内置命令，用于修改shell本身的行为。 我们对内置的set命令尤其感兴趣，因为此命令有几个选项可以帮助我们编写更安全的脚本。 我希望说服您，将set -euxo pipefail添加到您所有将来要编写的bash脚本的开头，是一个非常好的主意。

## set -e
当命令失败时，-e选项将导致bash脚本立即退出。 总的来说，这是对默认行为的巨大改进。在默认行为下，脚本仅仅是忽略失败的命令，并继续下一行。 此选项也足够聪明，不会对作为条件语句一部分的失败命令做出反应。 此外，对于一些少见的情况——即您不希望失败的命令触发程序的立即退出时，您也可以在命令后附加 ||，以便使其状态判定为 true。

#### Before

```shell
#!/bin/bash

# 'foo' is a non-existing command
foo
echo "bar"

# output
# ------
# line 4: foo: command not found
# bar
#
# Note how the script didn't exit when the foo command could not be found.
# Instead it continued on and echoed 'bar'.
```

#### After
```shell
#!/bin/bash
set -e

# 'foo' is a non-existing command
foo
echo "bar"

# output
# ------
# line 5: foo: command not found
#
# This time around the script exited immediately when the foo command wasn't found.
# Such behavior is much more in line with that of higher-level languages.
```

#### Any command returning a non-zero exit code will cause an immediate exit

```shell
#!/bin/bash
set -e

# 'ls' is an existing command, but giving it a nonsensical param will cause
# it to exit with exit code 1
$(ls foobar)
echo "bar"

# output
# ------
# ls: foobar: No such file or directory
#
# I'm putting this in here to illustrate that it's not just non-existing commands
# that will cause an immediate exit.
```

#### Preventing an immediate exit

```shell
#!/bin/bash
set -e

foo || true
$(ls foobar) || true
echo "bar"

# output
# ------
# line 4: foo: command not found
# ls: foobar: No such file or directory
# bar
#
# Sometimes we want to ensure that, even when 'set -e' is used, the failure of
# a particular command does not cause an immediate exit. We can use '|| true' for this.
```

#### Failing commands in a conditional statement will not cause an immediate exit

```shell
#!/bin/bash
set -e

# we make 'ls' exit with exit code 1 by giving it a nonsensical param
if ls foobar; then
  echo "foo"
else
  echo "bar"
fi

# output
# ------
# ls: foobar: No such file or directory
# bar
#
# Note that 'ls foobar' did not cause an immediate exit despite exiting with
# exit code 1. This is because the command was evaluated as part of a
# conditional statement.
```

这就是set -e的全部。 但是，仅靠-e设置还远远不够。 通过将set -e与set -o pipefail结合使用，我们可以进一步改善它的行为。 接下来让我们看看。

## set -o pipefail
bash shell通常只查看管道的最后一条命令的退出代码。 此行为并不理想，因为它导致-e选项只能对管道的最后一条命令的退出代码起作用。 解决这种情况，就需要用到 -o pipefail 选项。此特定选项，会将管道的退出代码设置为，最右命令的退出代码以非零的状态退出，或者如果管道的所有命令成功退出，才会设置为零。

#### Before

```shell
#!/bin/bash
set -e

# 'foo' is a non-existing command
foo | echo "a"
echo "bar"

# output
# ------
# a
# line 5: foo: command not found
# bar
#
# Note how the non-existing foo command does not cause an immediate exit, as
# it's non-zero exit code is ignored by piping it with '| echo "a"'.
```

#### After

```shell
#!/bin/bash
set -eo pipefail

# 'foo' is a non-existing command
foo | echo "a"
echo "bar"

# output
# ------
# a
# line 5: foo: command not found
#
# This time around the non-existing foo command causes an immediate exit, as
# '-o pipefail' will prevent piping from causing non-zero exit codes to be ignored.
```

希望本节明确指出，-o pipefail 相比于仅单独使用-e的情况下，提供了重要的改进。 但是，正如我们将在下一节中看到的那样，我们仍然可以做更多的工作来使脚本的行为像高级语言一样。

## set -u
此选项使bash shell将未设置的变量视为错误并立即退出。 未设置变量是shell脚本中出现错误的常见原因，因此，具有未设置的变量时，便立即退出，通常是非常可取的行为。

#### Before

```shell
#!/bin/bash
set -eo pipefail

echo $a
echo "bar"

# output
# ------
#
# bar
#
# The default behavior will not cause unset variables to trigger an immediate exit.
# In this particular example, echoing the non-existing $a variable will just cause
# an empty line to be printed.
```

#### After

```shell
#!/bin/bash
set -euo pipefail

echo "$a"
echo "bar"

# output
# ------
# line 5: a: unbound variable
#
# Notice how 'bar' no longer gets printed. We can clearly see that '-u' did indeed
# cause an immediate exit upon encountering an unset variable.
```

#### Dealing with ${a:-b} variable assignments
有时，您会希望使用 $ {a：-b}变量分配，来确保在 a 为空或未定义时，为变量分配默认值b。 -u 选项足够聪明，不会在这种情况下导致程序的立即退出。

```shell
#!/bin/bash
set -euo pipefail

DEFAULT=5
RESULT=${VAR:-$DEFAULT}
echo "$RESULT"

# output
# ------
# 5
#
# Even though VAR was not defined, the '-u' option realizes there's no need to cause
# an immediate exit in this scenario as a default value has been provided.
```

#### Using conditional statements that check if variables are set
有时，您希望脚本在遇到未设置的变量时不立即退出。 一个常见的示例，是在if语句中检查变量是否存在。

```shell
#!/bin/bash
set -euo pipefail

if [ -z "${MY_VAR:-}" ]; then
  echo "MY_VAR was not set"
fi

# output
# ------
# MY_VAR was not set
#
# In this scenario we don't want our program to exit when the unset MY_VAR variable
# is evaluated. We can prevent such an exit by using the same syntax as we did in the
# previous example, but this time around we specify no default value.
```

本节使我们更近一步地使得 bash shell 表现得像高级语言一样。 尽管-euo pipefail对于尽早发现各种问题非常有用，但有时还不够。 这就是为什么在下一节中，我们将介绍一个选项，该选项将帮助我们找出您偶尔遇到的，那些非常棘手的错误。

## set -x
-x选项使bash在执行每个命令之前将其打印出来。 尝试调试bash脚本失败时，这会是一个很大的帮助。 请注意，参数会在打印命令之前扩展，这将导致我们的日志，包含执行时出现的实际参数值！

```shell
#!/bin/bash
set -euxo pipefail

a=5
echo $a
echo "bar"

# output
# ------
# + a=5
# + echo 5
# 5
# + echo bar
# bar
```

这就是-x选项，非常简单，但是对于调试很有帮助。 接下来，我们将看一个我以前从未听说过的选项，是此博客的读者建议这样做的。

## Reader suggestion: set -E
Traps are pieces of code that fire when a bash script catches certain signals. Aside from the usual signals (e.g. SIGINT, SIGTERM, …), traps can also be used to catch special bash signals like EXIT, DEBUG, RETURN, and ERR. However, reader Kevin Gibbs pointed out that using -e without -E will cause an ERR trap to not fire in certain scenarios.
Traps 是当bash脚本捕获某些信号时触发的代码段。 除了通常的信号（例如SIGINT，SIGTERM等）之外，traps 还可以用于捕获特殊的bash信号，例如EXIT，DEBUG，RETURN和ERR。 但是，读者Kevin Gibbs指出，在不使用-E的情况下使用-e，将导致在某些情况下并不触发ERR trap。

#### Before
```shell
#!/bin/bash
set -euo pipefail

trap "echo ERR trap fired!" ERR

myfunc()
{
  # 'foo' is a non-existing command
  foo
}

myfunc
echo "bar"

# output
# ------
# line 9: foo: command not found
#
# Notice that while '-e' did indeed cause an immediate exit upon trying to execute
# the non-existing foo command, it did not case the ERR trap to be fired.
```

#### After

```shell
#!/bin/bash
set -Eeuo pipefail

trap "echo ERR trap fired!" ERR

myfunc()
{
  # 'foo' is a non-existing command
  foo
}

myfunc
echo "bar"

# output
# ------
# line 9: foo: command not found
# ERR trap fired!
#
# Not only do we still have an immediate exit, we can also clearly see that the
# ERR trap was actually fired now.
```

文档指出，如果我们希望ERR  traps被 shell 函数，命令替换，以及在子 shell 环境中执行的命令继承，则需要设置-E选项。 因为通常在这种情况下，通常不会继承ERR trap。

## 结论
我希望这篇文章能告诉您为什么使用 set -euxo pipefail（或 set -Eeuxo pipefail）是一个好主意。 如果您还有其他建议要提出，请告诉我，我很乐意将它们添加到此列表中。

## 参考资料
[Safer bash scripts with 'set -euxo pipefail'](https://vaneyckt.io/posts/safer_bash_scripts_with_set_euxo_pipefail/)

[set builtin command manual](https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html)

[${a:-b} variable assignment](https://unix.stackexchange.com/questions/122845/using-a-b-for-variable-assignment-in-scripts/122878)

[traps concept manual](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_12_02.html)
