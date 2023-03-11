# 如何写出安全的、基本功能完善的Bash脚本[翻译]

每个人或多或少总会碰到要使用并且自己完成编写一个最基础的Bash脚本的情况。真实情况是，没有人会说“哇哦，我喜欢写这些脚本”。所以这也是为什么很少有人在写的时候专注在这些脚本上。

我本身也不是一个Bash脚本专家，但是我会在本文中跟你展示一个最基础，最简单的安全脚本模板，会让你写的Bash脚本更加安全实用，你掌握了之后肯定会受益匪浅。

## Why scripting in Bash ?
其实关于Bash脚本最好的解释如下：

> The opposite of “it’s like riding a bike” is “it’s like programming in bash”.
A phrase which means that no matter how many times you do something, you will have to re-learn it every single time.
<p align="right">— Jake Wharton (@JakeWharton) </br>December 2, 2020

意思就是，跟骑自行车相反，无论做了多少次，每次都感觉像重新学一样。

但是Bash脚本语言和其他一些广受欢迎的语言，例如JavaScript一样，他们不会轻易突然消失，虽然Bash脚本语言不太可能成为业界的主流语言，但实际它就在我们周围，无处不在。

Bash就像继承了shell的衣钵一样，在每台linux上都可以看到它的身影，这可是大多数后端程序运行的环境，因此当你需要编写服务器的应用程序启动、CI/CD步骤或集成测试用的脚本，Bash就在那里等着你。

将几个命令粘在一起，将输出从一个传递到另一个，然后只启动一些可执行文件，Bash是众多方案中最简单的一个。虽然用其他语言编写更大、更复杂的脚本更有效果，但你不能指望Python、Ruby、fish或其他任何你认为最好的程序，可以在任何地方编译使用。所以在将其添加到某个prod server、Docker image或CI环境之前，往往会让人三思而后行。

当然啦，Bash还远远不够完美两个字。它的语法对初学者就像一个噩梦。错误处理也很困难。到处都是我们必须处理掉的陷阱。

## Bash script template
废话不多说，模板如下：

```shell
#!/usr/bin/env bash

set -Eeuo pipefail
trap cleanup SIGINT SIGTERM ERR EXIT

script_dir=$(cd "$(dirname "${BASH_SOURCE[0]}")" &>/dev/null && pwd -P)

usage() {
  cat <<EOF
Usage: $(basename "${BASH_SOURCE[0]}") [-h] [-v] [-f] -p param_value arg1 [arg2...]

Script description here.

Available options:

-h, --help      Print this help and exit
-v, --verbose   Print script debug info
-f, --flag      Some flag description
-p, --param     Some param description
EOF
  exit
}

cleanup() {
  trap - SIGINT SIGTERM ERR EXIT
  # script cleanup here
}

setup_colors() {
  if [[ -t 2 ]] && [[ -z "${NO_COLOR-}" ]] && [[ "${TERM-}" != "dumb" ]]; then
    NOFORMAT='\033[0m' RED='\033[0;31m' GREEN='\033[0;32m' ORANGE='\033[0;33m' BLUE='\033[0;34m' PURPLE='\033[0;35m' CYAN='\033[0;36m' YELLOW='\033[1;33m'
  else
    NOFORMAT='' RED='' GREEN='' ORANGE='' BLUE='' PURPLE='' CYAN='' YELLOW=''
  fi
}

msg() {
  echo >&2 -e "${1-}"
}

die() {
  local msg=$1
  local code=${2-1} # default exit status 1
  msg "$msg"
  exit "$code"
}

parse_params() {
  # default values of variables set from params
  flag=0
  param=''

  while :; do
    case "${1-}" in
    -h | --help) usage ;;
    -v | --verbose) set -x ;;
    --no-color) NO_COLOR=1 ;;
    -f | --flag) flag=1 ;; # example flag
    -p | --param) # example named parameter
      param="${2-}"
      shift
      ;;
    -?*) die "Unknown option: $1" ;;
    *) break ;;
    esac
    shift
  done

  args=("$@")

  # check required params and arguments
  [[ -z "${param-}" ]] && die "Missing required parameter: param"
  [[ ${#args[@]} -eq 0 ]] && die "Missing script arguments"

  return 0
}

parse_params "$@"
setup_colors

# script logic here

msg "${RED}Read parameters:${NOFORMAT}"
msg "- flag: ${flag}"
msg "- param: ${param}"
msg "- arguments: ${args[*]-}"
```

想法是不要太长。 我不想滚动500行才到脚本逻辑。 同时，我希望这个模板，能为任何脚本打下坚实的基础。 但是由于Bash缺少任何形式的依赖项管理，这使它变得不那么容易。

一种新的解决方案是，拥有一个包含所有样板代码和实用程序功能的单独脚本，并在开始时执行它。 不利之处是必须始终将第二个文件附加到任何地方，从而一路偏离 “simple Bash script” 的想法。 因此，我决定只在模板中放入我认为是最小的模板，以使其尽可能短。

下面我们更详细地来研究下它。

### Choose Bash
```shell
#!/usr/bin/env bash
```
传统方式是，脚本是从shebang（#!）开始的。 为了获得最佳兼容性，它直接引用 /usr/bin/env，而不是 /bin/bash。 其实即使如此，执行脚本甚至也会失败，见Stack Overflow 上的讨论 —— [Why is #!/usr/bin/env bash superior to #!/bin/bash?](https://stackoverflow.com/questions/21612980/why-is-usr-bin-env-bash-superior-to-bin-bash)

### Fail fast
```shell
set -Eeuo pipefail
```

set命令可以更改脚本执行选项。例如，**通常Bash不关心某个命令是否失败，返回非零退出状态代码。它只是快速地跳到下一个。**现在考虑一下这个小脚本：

```shell
#!/usr/bin/env bash
cp important_file ./backups/
rm important_file
```

如果备份目录不存在，会发生什么情况？确切地说，你将在控制台中收到一条错误消息，但是在你能够做出反应之前，该文件已经被第二个命令删除。

有关正确设置哪些选项的详细信息 set -Eeuo pipefail 更改了具体哪些设置的详细信息，以及它们是如何保护您的，请参考我近几年来都保存在个人书签中的文章—— [Safer bash scripts with 'set -euxo pipefail'](https://vaneyckt.io/posts/safer_bash_scripts_with_set_euxo_pipefail/)。

然而，你也应该知道也有些反对设置这些选项的说法。

### Get the location

```shell
script_dir=$(cd "$(dirname "${BASH_SOURCE[0]}")" &>/dev/null && pwd -P)
```

这行代码尽其所能定义脚本的位置目录，~~然后我们要切换那里么？为什么？~~

假设脚本目录也是一个工作目录，我们的脚本通常在相对于脚本位置的路径上运行，复制文件并执行命令。 而且，只要我们从脚本目录执行脚本即可。

但是，假设我们的CI配置执行如下所示的脚本呢：

```shell
/opt/ci/project/script.sh
```

那么我们的脚本便不是在项目目录中运行，而是在CI工具的某些完全不同的工作目录中运行。 我们可以通过执行脚本之前转到其所在目录来修复它：

```shell
cd /opt/ci/project && ./script.sh
```

但从脚本的角度解决这个问题要好得多。因此，如果脚本从同一目录中读取某个文件或执行另一个程序，请按如下方式调用：

```shell
cat "$script_dir/my_file"
```

同时，脚本不会更改工作目录的位置。如果脚本是从其他目录执行的，并且用户提供了某个文件的相对路径，则我们仍然可以读取该文件。

### Try to clean up

```shell
trap cleanup SIGINT SIGTERM ERR EXIT

cleanup() {
  trap - SIGINT SIGTERM ERR EXIT
  # script cleanup here
}
```

就像脚本的 finally 代码块中，类似 trap 之类的部分。 在脚本末尾，通常，因错误或外部信号导致，将执行cleanup（）函数。 在这里，您可以尝试删除脚本创建的所有临时文件。
在脚本结束时，将执行cleanup（）函数。你可以在这里尝试删除脚本创建的所有临时文件。

请记住，cleanup（）不仅可以在最后调用，在任何部分都可以。
只要记住，不仅可以在最后调用cleanup（），还可以让脚本完成工作的任何部分。 您尝试清除的所有资源，不一定都有存在的必要。

### Display helpful help

```shell
usage() {
  cat <<EOF
Usage: $(basename "${BASH_SOURCE[0]}") [-h] [-v] [-f] -p param_value arg1 [arg2...]

Script description here.

...
EOF
  exit
}
```

尽量让usage（）函数相对靠近脚本的顶部，有两种作用：

- **要为不知道所有选项并且不想查看整个脚本来发现这些选项的人显示帮助。**

- **当有人修改脚本时，保存一个最小的文档（因为两周后，你甚至不记得当初是怎么写的）。**

我不主张在这里记录每个函数。但是一个简短、漂亮的脚本使用这些消息是必需的。

### Print nice messages

```shell
setup_colors() {
  if [[ -t 2 ]] && [[ -z "${NO_COLOR-}" ]] && [[ "${TERM-}" != "dumb" ]]; then
    NOFORMAT='\033[0m' RED='\033[0;31m' GREEN='\033[0;32m' ORANGE='\033[0;33m' BLUE='\033[0;34m' PURPLE='\033[0;35m' CYAN='\033[0;36m' YELLOW='\033[1;33m'
  else
    NOFORMAT='' RED='' GREEN='' ORANGE='' BLUE='' PURPLE='' CYAN='' YELLOW=''
  fi
}

msg() {
  echo >&2 -e "${1-}"
}
```

首先，如果你不想在文本中使用颜色，那么先删除setup_colors（）函数。我保留它是因为我知道，如果我不必每次用到它都得用google查询的话，我就会更频繁地使用颜色功能。

其次，这些颜色只用于msg（）函数，而不是echo命令。

msg（）函数用于打印不是脚本输出的所有内容。这包括所有日志和消息，而不仅仅是错误。引用
12 Factor CLI Apps的文章说法：

> **In short: stdout is for output, stderr is for messaging.**
<p align="right">— Jeff Dickey, who knows a little about building CLI apps

**stdout用于输出，stderr用于消息传递。**

这就是为什么在大多数情况下你不应该为stdout使用颜色。

用msg（）打印的消息被发送到stderr流并支持特殊的序列，比如颜色。如果stderr输出不是交互式终端，或者传递了一个标准参数，那么颜色将被禁用。
用法如下：

```shell
msg "This is a ${RED}very important${NOFORMAT} message, but not a script output value!"
```

要检查stderr是不是交互式终端时的行为，请在脚本中添加类似于上面的一行。然后执行它，将stderr重定向到stdout并通过管道将其发送到cat。管道操作使输出不再直接发送到终端，而是发送到下一个命令，因此颜色会被禁用。

```shell
$ ./test.sh 2>&1 | cat
This is a very important message, but not a script output value!
```

### Parse any parameters

```shell
parse_params() {
  # default values of variables set from params
  flag=0
  param=''

  while :; do
    case "${1-}" in
    -h | --help) usage ;;
    -v | --verbose) set -x ;;
    --no-color) NO_COLOR=1 ;;
    -f | --flag) flag=1 ;; # example flag
    -p | --param) # example named parameter
      param="${2-}"
      shift
      ;;
    -?*) die "Unknown option: $1" ;;
    *) break ;;
    esac
    shift
  done

  args=("$@")

  # check required params and arguments
  [[ -z "${param-}" ]] && die "Missing required parameter: param"
  [[ ${#args[@]} -eq 0 ]] && die "Missing script arguments"

  return 0
}
```

如果在脚本中参数化有意义的话，我就通常就会去做，即使整个脚本只在一个地方使用。它使复制和重用它变得更容易，而这通常是早晚发生的。而且，即使某些东西需要硬编码，通常在比Bash脚本更高的级别上有更好的位置。

**CLI参数有三种主要类型：标志、命名参数和位置参数。** parse_params（）函数支持所有这些参数。

这里没有处理的唯一一个公共参数模式是连接多个单字母标志。为了能够传递两个标志作为-ab，而不是-a -b，需要一些额外的代码。

while循环是一种手动解析参数的方法。在其他语言中，您应该使用一个内置的解析器或可用的库，但是，好吧，这是Bash。

模板中有一个示例标志（-f）和命名参数（-p）。只需更改或复制它们以添加其他参数。之后不要忘记更新usage（）。

这里最重要的一点是，当您使用第一个google结果进行Bash参数解析时，通常会丢失一个未知选项的错误。脚本收到未知选项的事实意味着用户希望它执行脚本无法完成的操作。所以用户的期望和脚本行为可能会有很大的不同。最好是在坏事发生之前完全阻止处决。

在Bash中解析参数有两种选择。是一个接一个的。有人赞成和反对使用它们。我发现这些工具不是最好的，因为默认情况下，macOS上的getopt行为完全不同，getopts不支持长参数（比如–help）。

## Using the template
直接复制粘贴它，就像你在网上找到的大多数代码一样。

复制后，只需更改4件事：

- 包含脚本说明的usage（）文本
- cleanup（）的内容
- parse_params（）中的参数–保留–help和–no color，但替换示例：-f和-p
- 实际的脚本逻辑

## Portability
我在MacOS上测试了这个模板（使用默认的bash3.2）和几个Docker映像：Debian、Ubuntu、CentOS、amazonlinux、Fedora。它的确起作用了。

显然，它不能在缺少Bash的环境（如Alpine Linux）上使用。 作为一种极简主义的系统，Alpine 使用非常轻巧的 ash（即 Almquist shell）。

您可能会问，如果使用兼容Bourne shell的脚本（几乎可以在任何地方使用）是否更好。 至少在我看来，答案是否定的。 Bash更安全，功能更强大（但仍不容易使用），因此，我可以接受我几乎不用的，少数的 Linux 发行版，缺乏对 Bash的支持。

## Further reading
在用Bash或其他更好的语言创建CLI脚本时，有一些通用规则。这些资源将指导您如何使小型脚本和大型CLI应用程序可靠，参考如下：

- [Command Line Interface Guidelines](https://clig.dev/)
- [12 Factor CLI Apps](https://medium.com/@jdxcode/12-factor-cli-apps-dd3c227a0e46)
- [Command line arguments anatomy explained with examples](https://betterdev.blog/command-line-arguments-anatomy-explained/)

## Closing notes
我不会是第一个也不是最后一个创建Bash脚本模板的人。这个项目是一个很好的选择，虽然对我的日常需求来说有点太大了。毕竟，我尽量使Bash脚本尽可能小（而且很少使用）。

编写Bash脚本时，请使用支持ShellCheck linter的IDE，如JetBrains IDEs。它会阻止你做一堆适得其反的事情。


## 参考资料

[Minimal safe Bash script template](https://betterdev.blog/minimal-safe-bash-script-template/)

[Github: ralish/bash-script-template](https://github.com/ralish/bash-script-template)

[Safer bash scripts with 'set -euxo pipefail'](https://vaneyckt.io/posts/safer_bash_scripts_with_set_euxo_pipefail/)

## 小白点评
作者写的很好，直接复制模板，自定义使用即可。不过 set 的部分没有另一篇 bash strict mode 解释的详尽。
</br>译文不够通顺，建议有个大致了解后，最好去通读一下原文，以及其推荐的文章。
</br>这个彩色输出啊，大概就是那种很想用，但却怎么也记不住具体写法的知识点。