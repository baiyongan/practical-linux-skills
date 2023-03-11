# 04 | 快速上手几个Linux命令：每家公司都有自己的黑话

## 用户与密码

- 修改密码 passwd

- 添加用户 useradd

- 添加用户组 

- 对应文件 
  - 账户文件 /etc/passwd 
  - 组文件 /etc/group

## 浏览文件

- cd
- ls

## 安装软件

 Linux 现在常用的有两大体系，一个是 CentOS 体系，一个是 Ubuntu 体系，前者使用 rpm，后者使用 deb。


```shell
# 安装 xxx 软件
## CentOS: 
rpm -i xxx.rpm

## Ubuntu:
dpkg -i xxx.deb
```

```shell
# 查看安装的 xxx 软件
## CentOS: 
rpm -qa | grep xxx

## Ubuntu:
dpkg -l | grep xxx
```

> [!TIP]
> 如果不知道关键词，可以使用 `rpm -qa | more` 和 `rpm -qa | less` 这两个命令，他们可以将很长的结果分页展示出来，便于查找。

```shell
# 删除安装的 xxx 软件
## CentOS: 
rpm -e xxx
## e 即 erase

## Ubuntu:
dpkg -r grep xxx
## r 即 remove
```

以上都是不使用包管理器时对软件包的操作，Linux 其实也有自己的包管理器，即类似于 windows 中的软件管家，CentOS 下面是 yum，Ubuntu 下面是 apt-get。

### CentOS: yum

- yum install xxx
- yum erase xxx
- yum 源配置文件：/etc/yum.repos.d/CentOS-Base.repo

```shell
[base]
name=CentOS-$releasever - Base - 163.com
baseurl=http://mirrors.163.com/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7
```

### Ubuntu: apt-get

- apt-get install xxx
- apt-get pruge xxx
- apt 配置文件：/etc/apt/sources.list

```shell
deb http://mirrors.163.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ xenial-proposed main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ xenial-backports main restricted universe multiverse
```

**其实无论是先下载再安装，还是通过软件管家进行安装，都是下载一些文件，然后将这些文件放在某个路径下，然后在相应的配置文件中配置一下。**

例如，在 Windows 里面，最终会变成 C:\Program Files 下面的一个文件夹以及注册表里面的一些配置。对应 Linux 里面会放的更散一点。例如，主执行文件会放在 /usr/bin 或者 /usr/sbin 下面，其他的库文件会放在 /var 下面，配置文件会放在 /etc 下面。

## 运行程序

### 交互式命令行运行

Linux 不是根据后缀名来执行的。它的执行条件是这样的：只要文件有 x 执行权限，都能到文件所在的目录下，通过./filename运行这个程序。当然，如果放在 PATH 里设置的路径下面，就不用./ 了，直接输入文件名就可以运行了，Linux 会帮你找。

这是 Linux 执行程序最常用的一种方式。这样执行的程序可能需要和用户进行交互，例如允许让用户输入，然后输出结果也打印到交互命令行上。比较适合运行一些简单的命令，例如通过 `date` 获取当前时间。这种模式的缺点是，一旦当前的交互命令行退出，程序就停止运行了。

### 后台运行

`nohup` 命令，意思是 no hang up（不挂起）。也就是说，当前交互命令行退出的时候，程序还要在。

这个时候，程序不能霸占交互命令行，而是应该在后台运行。最后加一个 &，就表示后台运行。

一般形式：

```shell
nohup command >out.file 2>&1 &
## 其中 “1”表示文件描述符 1，表示标准输出，“2”表示文件描述符 2，意思是标准错误输出，“2>&1”表示标准输出和错误输出合并，并合并到 out.file 里
```

如何关闭这个进程？假设启动的程序包含某个关键字，则可以使用如下的命令：

```shell
ps -ef | grep 关键字  | awk '{print $2}'| xargs kill -9
## ps -ef 可以单独执行，列出所有正在运行的程序；
## grep 通过关键字找到启动的程序；
## awk 工具可以很灵活地对文本进行处理，这里的 awk '{print $2}'是指第二列的内容，是运行的程序 ID；
## xargs 将程序 ID 传递给 kill -9，也就是发给这个运行的程序一个信号，让它关闭。
## 如果你已经知道运行的程序 ID，可以直接使用 kill 关闭运行的程序。
```

### 以服务的方式运行

例如在 Ubuntu 中，通过命令 `systemctl start mysql` 启动 MySQL，通过 `systemctl enable mysql` 设置开机启动。

之所以成为服务并且能够开机启动，是因为在 /lib/systemd/system 目录下会创建一个 XXX.service 的配置文件，里面定义了如何启动、如何关闭。

同理，对于 CentOS，会在 /usr/lib/systemd/system 目录下，创建一个 XXX.service 的配置文件，从而成为一个服务。


## 关机与重启

- shutdown -h now 是现在就关机
- reboot 是重启。

![04_01_linux_common_command.png](https://i.loli.net/2021/07/09/P2YJB5lWKswDVTb.png)

> [!ATTENTION]
> ubuntu 中现在用apt，apt = apt-get、apt-cache 和 apt-config 中最常用命令选项的集合。






















