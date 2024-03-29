# 性能优化实战

## 导师简介

倪鹏飞，微软 Azure 资深工程师，同时是 Kubernetes 项目的维护者，主要负责开源容器编排系统 Kubernetes 在 Azure 的落地实践。

!!! tips
    - **blog :** [Feisky](https://feisky.xyz/)
    - **github :** [feiskyer](https://github.com/feiskyer) 

!!! quote
    把观察到的性能问题跟系统原理关联起来，特别是把系统从应用程序、库函数、系统调用、再到内核和硬件等不同的层级贯穿起来。

性能问题为什么这么难呢？主要是因为性能优化是一个系统工程，总是牵一发而动全身。它涉及了从程序设计、算法分析、编程语言，再到系统、存储、网络等各种底层基础设施的方方面面。每一个组件都有可能出问题，而且很可能多个组件同时出问题。

最好的学习方式一定是带着问题学习，而不是先去啃那几本厚厚的原理书籍，这样很容易把自己的信心压垮。

### 学习要会抓重点

其实只需要了解少数几个系统组件的基本原理和协作方式，掌握基本的性能指标和工具，学会实际工作中性能优化的常用技巧，就已经可以准确分析和优化大多数的性能问题了。在这个认知的基础上，再反过来去阅读那些经典的操作系统或者其他书籍，才能事半功倍。

## 课程目录

![00_01_performance_tuning_content.jpg](https://i.loli.net/2021/03/04/po3XAzvxEaib2Lm.jpg)

## 课程简介

!!! note
    - **课程代码仓库 ：** [feiskyer/linux-perf-examples](https://github.com/feiskyer/linux-perf-examples)
    - **课程官网 ：** [Linux性能优化实战-10分钟帮你找到系统瓶颈](https://time.geekbang.org/column/intro/140)

### 课程模块

主要有 5 个模块：

**1. CPU性能模块**

**2. 磁盘I/O性能模块**

**3. 内存性能模块**

**4. 网络性能模块**

**5. 综合实战模块**

前四个模块，从资源使用的角度出发，分析各种 Linux 资源可能会碰到的性能问题，每个模块由浅入深划分为四个不同的篇章。最后一个模块则还原真实的工作场景，把之前学到的所有知识融汇贯通。

其中，篇章划分如下：

- **基础篇**

介绍Linux必备的基本原理以及对应的性能指标和性能工具。

- **案例篇**

通过模拟案例，分析高手再遇到资源瓶颈时，是如何观测、定位、分析并优化这些性能问题的

- **套路篇**

在理解了基础，体验了模拟案例后，梳理出排查问题的整体思路，也就是检查性能问题的一般步骤。

- **答疑篇**

共性问题的系统解答