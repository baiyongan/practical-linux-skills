# 03 | 你可以把Linux内核当成一家软件外包公司的老板

## 一台电脑的基本硬件组成

![03_01_computer_hardware.png](https://i.loli.net/2021/07/07/6NDdIv83mSlPc4F.png)

!!! note
    操作系统其实就像一个软件外包公司，其内核就相当于这家外包公司的老板。可以将自己的角色切换成这家软件外包公司的老板，设身处地地去理解操作系统是如何协调各种资源，帮客户做成事情的。

    本课程所说的“用户”，都是指操作系统的用户，“客户”则是指外包公司的客户。

## 操作系统 vs 外包公司

### 操作系统

- 鼠标和键盘是计算机的输入设备。
- 屏幕，也就是显示器，是计算机的输出设备

### 外包公司

- 销售、售前等角色，专门负责和客户对接，把客户需求拿回来，这些人统称为客户对接员。
- 交付人员角色，即当客户给你提了需求，不管你做还是不做，最终做成什么样，你都需要给客户反馈，将做好的需求展示给他们看。

在操作系统中，输入设备驱动其实就是客户对接员。在操作系统中，显卡有显卡驱动，称为输出设备驱动，也就是上面说的交付人员。

## 操作系统的部分子系统

### 文件管理子系统

电脑上的程序有很多，如chrome程序、Word 程序等等，它们都以二进制文件的形式保存在硬盘上。硬盘是个物理设备，要按照规定格式化成为文件系统，才能存放这些程序。

文件系统需要一个系统进行统一管理，称为文件管理子系统（File Management Subsystem）。

### 进程管理子系统

二进制文件是静态的，称为程序（Program），而运行起来的程序，称为进程（Process）。

任何一个程序要想运行起来，就需要调用系统调用，创建进程。

在操作系统中，进程的执行也需要分配 CPU 进行执行，也就是按照程序里面的二进制代码一行一行地执行。于是，为了管理进程，还需要一个进程管理子系统（Process Management Subsystem）。如果运行的进程很多，则一个 CPU 会并发运行多个进程，也就需要 CPU 的调度能力了。

### 内存管理子系统

操作系统中，不同的进程有不同的内存空间，但是整个电脑内存容量是固定的，所以需要统一的管理和分配，这就需要内存管理子系统（Memory Management Subsystem）。

!!! tips
    如果想直观地了解某程序如何使用 CPU 和内存，可以打开任务管理器，你就能看到某程序的相应进程耗费的 CPU 和内存。

![03_02_system_architecture_vs_company.png](https://i.loli.net/2021/07/07/cyGMxrYAv6isOaT.png)

## 操作系统架构概览

![03_03_system_kernel_architechture.png](https://i.loli.net/2021/07/07/dgUFiWBIaYTpVyx.png)

## 拓展阅读

[Linux Source Code](https://github.com/torvalds/linux)

[The Linux Kernel Archives](https://www.kernel.org/)

[Overview of the Kernel Source](https://courses.linuxchix.org/kernel-hacking-2002/08-overview-kernel-source.html)

!!! note
    内核版本5.0.7，目录结构：

    - kernel 内核管理核心代码-进程管理子系统
    - mm 内存管理代码-内存管理子系统
    - drivers 驱动程序代码-设备管理子系统
    - fs 文件系统代码-文件管理子系统
    - net 网络协议代码-网络管理子系统
    - arch 体系结构代码，如x86、arm等
    - ipc 进程间通信子系统
    - init Linux系统启动初始化相关代码