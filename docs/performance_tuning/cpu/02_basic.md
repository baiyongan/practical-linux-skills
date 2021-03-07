# 基础篇：到底应该怎么理解“平均负载”？

> [!TIP]
> **遇到问题，特别是基础问题时，要多问自己几个为什么，以求能彻底理解现象背后的本质原理，这样的话，用起来才更灵活，也更有底气。**

## 平均负载概念

平均负载，即 Load Average，是一个最常见，也是最重要的系统指标。

> [!ATTENTION]
> 简单来说，平均负载时指单位时间内，系统处于**不可运行状态**和**不可中断状态**的平均进程数，也就是**平均活跃进程数**，它和CPU使用率没有直接关系。

### 查看工具: uptime

```shell
$ uptime
02:34:03 up 2 days, 20:14,  1 user,  load average: 0.63, 0.83, 0.88

# 其中
#02:34:03              //当前时间
#up 2 days, 20:14      //系统运行时间
#1 user                //正在登录的用户数
#最后三个数字，依次则是过去1分钟、5分钟、15分钟的平均负载（Load Average）
```

### 可运行状态和不可中断状态

- 所谓可运行状态的进程，是指正在使用CPU或者正在等待CPU的进程，也就是我们常用ps命令看到的，处于R状态（Running 或 Runnable）的进程。
- 不可中断状态的进程则是正处于内核态关键流程中的进程，并且这些流程是不可打断的，比如最常见的是等待硬件设备的I/O响应，也就是我们在ps命令中看到的D状态（Uninterruptible Sleep，也称为Disk Sleep）的进程。

比如，当一个进程向磁盘读写数据时，为了保证数据的一致性，在得到磁盘回复前，它是不能被其他进程或者中断打断的，这个时候的进程就处于不可中断状态。如果此时的进程被打断了，就容易出现磁盘数据与进程数据不一致的问题。所以，

**`不可中断状态实际上是系统对进程和硬件设备的一种保护机制。`**

因此，你可以简单理解为，平均负载其实就是平均活跃进程数。平均活跃进程数，直观上的理解就是单位时间内的活跃进程数，但它实际上是活跃进程数的指数衰减平均值。这个“指数衰减平均”的详细含义你不用计较，这只是系统的一种更快速的计算方式，你把它直接当成活跃进程数的平均值也没问题。

### 实例解释：当平均负载为2时，意味着什么呢？

- 在只有2个CPU的系统上，意味着所有的CPU都刚好被完全占用。
- 在4个CPU的系统上，意味着CPU有50%的空闲。
- 而在只有1个CPU的系统中，则意味着有一半的进程竞争不到CPU。

## 平均负载为多少时合理？

### 先确定系统 CPU 个数
平均负载最理想的情况是等于 CPU个数。所以在评判平均负载时，**首先你要知道系统有几个 CPU**，这可以通过 top 命令或者从文件 /proc/cpuinfo 中读取，比如：

```shell
$ grep 'model name' /proc/cpuinfo | wc -l
2
# 或者命令行输入 top 后，按数字 1 查看 CPU 具体信息
```

有了CPU 个数，我们就可以判断出，当平均负载比 CPU 个数还大的时候，系统已经出现了过载。

### 三个平均负载值要参考哪个？

**都要看**。三个不同时间间隔的平均值，其实给我们提供了，**分析系统负载趋势**的数据来源，让我们能更全面、更立体地理解目前的负载状况。

- 如果1分钟、5分钟、15分钟的三个值基本相同，或者相差不大，那就说明系统负载很平稳。
- 但如果1分钟的值远小于15分钟的值，就说明系统最近1分钟的负载在减少，而过去15分钟内却有很大的负载。
- 反过来，如果1分钟的值远大于 15 分钟的值，就说明最近1分钟的负载在增加，这种增加有可能只是临时性的，也有可能还会持续增加下去，所以就需要持续观察。一旦1分钟的平均负载接近或超过了CPU的个数，就意味着系统正在发生过载的问题，这时就得分析调查是哪里导致的问题，并要想办法优化了。

举个例子，假设我们在一个单 CPU 系统上看到平均负载为 1.73，0.60，7.98，那么说明在过去 1 分钟内，系统有 73% 的超载，而在 15 分钟内，有 698% 的超载，从整体趋势来看，系统的负载在降低。

### 实际生产环境经验

Q: 在实际生产环境中，平均负载多高时，需要我们重点关注呢？
A: **当平均负载高于 CPU 数量70%的时候**，你就应该分析排查负载高的问题了。一旦负载过高，就可能导致进程响应变慢，进而影响服务的正常功能。
但70%这个数字并不是绝对的，最推荐的方法，还是把系统的平均负载监控起来，然后根据更多的历史数据，判断负载的变化趋势。当发现负载有明显升高趋势时，比如说负载翻倍了，你再去做分析和调查。

## 平均负载与CPU使用率

Q: 既然平均负载代表的是活跃进程数，那平均负载高了，不就意味着 CPU 使用率高吗？
A: 还是要回到平均负载的含义上来，平均负载是指单位时间内，处于可运行状态和不可中断状态的进程数。所以，它不仅包括了正在使用 CPU 的进程，还包括等待 CPU 和等待 I/O 的进程。
而 CPU 使用率，是单位时间内 CPU 繁忙情况的统计，跟平均负载并不一定完全对应。比如：

- CPU 密集型进程，使用大量 CPU 会导致平均负载升高，此时这两者是一致的；
- I/O 密集型进程，等待 I/O 也会导致平均负载升高，但 CPU 使用率不一定很高；
- 大量等待 CPU 的进程调度也会导致平均负载升高，此时的CPU使用率也会比较高。

## 平均负载案例分析

### 环境准备与工具选用

课程使用的案例环境配置如下：
- 机器配置：2 CPU，8GB 内存。
- 预先安装 stress 和 sysstat 包，如 apt install stress sysstat。
- 默认使用 root 用户运行。

### 工具介绍

- stress：是一个 Linux 系统压力测试工具，在这里用作异常进程模拟平均负载升高的场景。
- sysstat： 包含了常用的 Linux 性能工具，用来监控和分析系统的性能。案例会用到这个包的两个命令 mpstat 和 pidstat。
  - mpstat： 是一个常用的多核 CPU 性能分析工具，用来实时查看每个 CPU 的性能指标，以及所有CPU的平均指标。
  - pidstat： 是一个常用的进程性能分析工具，用来实时查看进程的 CPU、内存、I/O 以及上下文切换等性能指标。

### 测试准备

安装略，自行 google 解决。以下每个场景，都开三个终端，登录到同一台 Linux 机器中。
先用 uptime 命令，看一下测试前的平均负载情况：
```shell
$ uptime
...,  load average: 0.11, 0.15, 0.09
```

### 场景一：CPU 密集型进程

#### 测试

```shell
#在第一个终端运行 stress 命令，模拟一个 CPU 使用率 100% 的场景
$ stress --cpu 1 --timeout 600

#在第二个终端运行 uptime 查看平均负载的变化情况：
# -d 参数表示高亮显示变化的区域
$ watch -d uptime
...,  load average: 1.00, 0.75, 0.39

#在第三个终端运行 mpstat 查看 CPU 使用率的变化情况
# -P ALL 表示监控所有CPU，后面数字5表示间隔5秒后输出一组数据
$ mpstat -P ALL 5
Linux 4.15.0 (ubuntu) 09/22/18 _x86_64_ (2 CPU)
13:30:06     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
13:30:11     all   50.05    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00   49.95
13:30:11       0    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
13:30:11       1  100.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00
```

#### 分析

从终端二中可以看到，1 分钟的平均负载会慢慢增加到 1.00，而从终端三中还可以看到，正好有一个 CPU 的使用率为 100%，但它的 iowait 只有 0。这说明，平均负载的升高正是由于 CPU 使用率为 100% 。
到底是哪个进程导致了 CPU 使用率为 100% 呢？你可以使用 pidstat 来查询：
从下面结果可以明显看到，stress进程的CPU使用率为100%。

```shell
# 间隔5秒后输出一组数据
$ pidstat -u 5 1
13:37:07      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
13:37:12        0      2962  100.00    0.00    0.00    0.00  100.00     1  stress
```

### 场景二： I/O密集型进程

#### 测试

```shell
# 首先运行 stress 命令，模拟 I/O 压力，即不停地执行 sync
$ stress -i 1 --timeout 600

#在第二个终端运行uptime查看平均负载的变化情况：
$ watch -d uptime
...,  load average: 1.06, 0.58, 0.37

# 第三个终端运行mpstat查看 CPU 使用率的变化情况：
# 显示所有CPU的指标，并在间隔5秒输出一组数据
$ mpstat -P ALL 5 1
Linux 4.15.0 (ubuntu)     09/22/18     _x86_64_    (2 CPU)
13:41:28     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
13:41:33     all    0.21    0.00   12.07   32.67    0.00    0.21    0.00    0.00    0.00   54.84
13:41:33       0    0.43    0.00   23.87   67.53    0.00    0.43    0.00    0.00    0.00    7.74
13:41:33       1    0.00    0.00    0.81    0.20    0.00    0.00    0.00    0.00    0.00   98.99
```

#### 分析

可以看到，1 分钟的平均负载会慢慢增加到 1.06，其中一个 CPU 的系统CPU使用率升高到了 23.87，而 iowait 高达 67.53%。这说明，平均负载的升高是由于 iowait 的升高。
到底是哪个进程，导致 iowait 这么高呢？还是用 pidstat 来查询：
如下，可以发现，还是 stress 进程导致的。

```shell
# 间隔5秒后输出一组数据，-u表示CPU指标
$ pidstat -u 5 1
Linux 4.15.0 (ubuntu)     09/22/18     _x86_64_    (2 CPU)
13:42:08      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
13:42:13        0       104    0.00    3.39    0.00    0.00    3.39     1  kworker/1:1H
13:42:13        0       109    0.00    0.40    0.00    0.00    0.40     0  kworker/0:1H
13:42:13        0      2997    2.00   35.53    0.00    3.99   37.52     1  stress
13:42:13        0      3057    0.00    0.40    0.00    0.00    0.40     0  pidstat
```


### 场景三：大量进程的场景

**当系统中运行进程超出 CPU 运行能力时，就会出现等待 CPU 的进程。**

#### 测试

```shell
# 首先，使用 stress，模拟 8 个进程：
$ stress -c 8 --timeout 600

# 在第二个终端运行uptime查看平均负载的变化情况：
# 由于系统只有 2 个CPU，明显比 8 个进程要少得多，因而，系统的 CPU 处于严重过载状态，平均负载高达7.97：
$ uptime
...,  load average: 7.97, 5.93, 3.02
```

#### 分析

运行pidstat来看一下进程的情况

```shell
# 间隔5秒后输出一组数据
$ pidstat -u 5 1
14:23:25      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
14:23:30        0      3190   25.00    0.00    0.00   74.80   25.00     0  stress
14:23:30        0      3191   25.00    0.00    0.00   75.20   25.00     0  stress
14:23:30        0      3192   25.00    0.00    0.00   74.80   25.00     1  stress
14:23:30        0      3193   25.00    0.00    0.00   75.00   25.00     1  stress
14:23:30        0      3194   24.80    0.00    0.00   74.60   24.80     0  stress
14:23:30        0      3195   24.80    0.00    0.00   75.00   24.80     0  stress
14:23:30        0      3196   24.80    0.00    0.00   74.60   24.80     1  stress
14:23:30        0      3197   24.80    0.00    0.00   74.80   24.80     1  stress
14:23:30        0      3200    0.00    0.20    0.00    0.20    0.20     0  pidstat
```

可以看出，8 个进程在争抢 2 个 CPU，每个进程等待 CPU 的时间（也就是代码块中的 %wait 列）高达 75%。这些超出 CPU 计算能力的进程，最终导致 CPU 过载。

## 小结

> [!NOTE]
> 平均负载提供了一个快速查看系统整体性能的手段，反映了整体的负载情况。但只看平均负载本身，我们并不能直接发现，到底是哪里出现了瓶颈。所以，在理解平均负载时，也要注意：
> - 平均负载高有可能是 CPU 密集型进程导致的；
> - 平均负载高并不一定代表 CPU 使用率高，还有可能是 I/O 更繁忙了；
> - 当发现负载高的时候，你可以使用 mpstat、pidstat 等工具，辅助分析负载的来源。

## 讨论整理

- iowait无法升高的问题，是因为案例中stress使用的是 sync() 系统调用，它的作用是刷新缓冲区内存到磁盘中。对于新安装的虚拟机，缓冲区可能比较小，无法产生大的IO压力，这样大部分就都是系统调用的消耗了。所以，你会看到只有系统CPU使用率升高。解决方法是使用stress的下一代stress-ng，它支持更丰富的选项，比如 stress-ng -i 1 --hdd 1 --timeout 600（--hdd表示读写临时文件）。

- pidstat输出中没有%wait的问题，是因为CentOS默认的sysstat稍微有点老，源码或者RPM升级到11.5.5版本以后就可以看到了。而Ubuntu的包一般都比较新，没有这个问题。

- mpstat无法观测的问题，案例中是等待5秒后输出1次结果就停止了，更好的做法是持续监控一段时间，比如持续观测20次：mpstat -P ALL 5 20。

- 可以用htop看负载，因为它更直接（在F2配置中勾选所有开关项，打开颜色区分功能），不同的负载会用不同的颜色标识。比如cpu密集型的应用，它的负载颜色是绿色偏高，iowait的操作，它的负载颜色是红色偏高等等，根据这些指标再用htop的sort就很容易定位到有问题的进程。还有个更好用的atop命令，好像是基于sar的统计生成的报告，直接就把有问题的进程标红了，更直观。

- Q: io高的例子 ，为何还是通过pidstat 看cpu？不应该是看哪个进程io高吗？
  - A：更好的方法是进程的io情况，比如可以试试pidstat -d

- 在 sched/loadavg.c 中计算平均值的算法为EMA，这种算法的目的主要是“距离目标预测窗口越近，则数据的价值越高，对未来影响越大”。
如果说“更快的计算”应该只有里面的 fixed_power_int 函数用 O(log n) 的时间来算 x^n。
所以内核中用 EMA 来算 loadavg 本质上并不是增加计算性能，而是让 loadavg 的趋势化更明显

- Q:现在大多数CPU有超线程能力，在计算和评估平均负载的时候，CPU的核数是指物理核数，还是超线程功能的逻辑核数？
  - A: 逻辑核数

- 将CPU比喻成一辆地铁，这个地铁的乘客容量就是CPU个数，正在使用CPU的进程就是在地铁上的人；等待CPU的进程就是在下一站等地铁来的人；等待I/O的进程就是在下一站要上车和下车的人，虽然现在对CPU没影响，可未来会影响，所以也要考虑到平均负载上。

- **工具的使用最好先查一查手册，网络上的搜索结果不一定完全准确。**

- Centos7系统安装stress（Linux系统压力测试工具）和sysstat（Linux性能工具）
  - yum install stress 一直找不到镜像处理方式 所以用了rpm方式安装，用rpm方式安装，先从下面的地址下载rpm包 http://ftp.tu-chemnitz.de/pub/linux/dag/redhat/el7/en/x86_64/rpmforge/RPMS/stress-1.0.2-1.el7.rf.x86_64.rpm ，
  然后 rpm -Uvh stress-1.0.2-1.el7.rf.x86_64.rpm 安装 
  - sysstat使用yum安装 yum install sysstat