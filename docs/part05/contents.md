# 目录

## 开篇

- [x] [01 | 如何学习 Linux 性能优化？](performance_tuning/how_to/01_overview.md)

## CPU 性能 13讲

- [x] [02 | 基础篇：平均负载](performance_tuning/cpu/02_basic.md)
- [x] [03 | 基础篇：上下文切换-上](performance_tuning/cpu/03_basic.md)
- [x] [04 | 基础篇：上下文切换-下](performance_tuning/cpu/04_basic.md)
- [x] [05 | 基础篇：CPU 使用率排查-上](performance_tuning/cpu/05_basic.md)
- [ ] [06 | 案例篇：CPU 使用率排查-下](performance_tuning/cpu/06_case.md)
- [ ] [07 | 案例篇：处理不可中断进程和僵尸进程-上](performance_tuning/cpu/07_case.md)
- [ ] [08 | 案例篇：处理不可中断进程和僵尸进程-下](performance_tuning/cpu/08_case.md)
- [ ] [09 | 基础篇：Linux 软中断](performance_tuning/cpu/09_basic.md)
- [ ] [10 | 案例篇：Linux 软中断性能问题](performance_tuning/cpu/10_case.md)
- [x] [11 | 套路篇：如何迅速分析系统 CPU 的瓶颈](performance_tuning/cpu/11_routine.md)
- [x] [12 | 套路篇：CPU 性能优化的思路](performance_tuning/cpu/12_routine.md)
- [ ] [13 | 答疑篇：无法模拟出 RES 中断的问题，怎么办？](performance_tuning/cpu/13_QA.md)
- [ ] [14 | 答疑篇：如何用 perf 工具分析 Java 程序？](performance_tuning/cpu/14_QA.md)

## Memory 性能 8讲

- [x] [15 | 基础篇：Linux 内存如何工作](performance_tuning/memory/15_basic.md)
- [x] [16 | 基础篇：理解内存中的 Buffer 和 Cache](performance_tuning/memory/16_basic.md)
- [ ] [17 | 案例篇：利用系统缓存优化程序的运行效率](performance_tuning/memory/17_case.md)
- [ ] [18 | 案例篇：内存泄漏的定位和处理](performance_tuning/memory/18_case.md)
- [ ] [19 | 案例篇：分析系统 Swap 变高原因-上](performance_tuning/memory/19_case.md)
- [ ] [20 | 案例篇：分析系统 Swap 变高原因-下](performance_tuning/memory/20_case.md)
- [ ] [21 | 套路篇：“快准狠”定位系统内存的问题](performance_tuning/memory/21_routine.md)
- [ ] [22 | 答疑篇：文件系统与磁盘的区别](performance_tuning/memory/22_QA.md)

## Disk I/O 性能 10讲

- [ ] [23 | 基础篇：Linux 文件系统如何工作](performance_tuning/disk_io/23_basic.md)
- [ ] [24 | 基础篇：Linux 磁盘 I/O 如何工作-上](performance_tuning/disk_io/24_basic.md)
- [ ] [25 | 基础篇：Linux 磁盘 I/O 如何工作-下](performance_tuning/disk_io/25_basic.md)
- [ ] [26 | 案例篇：如何找出狂打日志的“内鬼”？](performance_tuning/disk_io/26_case.md)
- [ ] [27 | 案例篇：为什么磁盘 I/O 延迟很高？](performance_tuning/disk_io/27_case.md)
- [ ] [28 | 案例篇：分析优化 SQL 查询时间](performance_tuning/disk_io/28_case.md)
- [ ] [29 | 案例篇：解决 Redis 响应严重延迟](performance_tuning/disk_io/29_case.md)
- [ ] [30 | 套路篇：如何迅速分析出系统 I/O 的瓶颈](performance_tuning/disk_io/30_routine.md)
- [ ] [31 | 套路篇：磁盘 I/O 性能优化的几个思路](performance_tuning/disk_io/31_routine.md)
- [ ] [32 | 答疑篇：阻塞、非阻塞 I/O 与同步、异步 I/O 的区别和联系](performance_tuning/disk_io/32_QA.md)
- [ ] [33 | 书单推荐：性能优化和 Linux 系统原理](performance_tuning/disk_io/33_explore.md)

## Network 性能 13讲

- [ ] [34 | 基础篇：Linux 网络必知-上](performance_tuning/network/34_basic.md)
- [ ] [35 | 基础篇：Linux 网络必知-下](performance_tuning/network/35_basic.md)
- [ ] [36 | 基础篇：C10K 和 C1000K 回顾](performance_tuning/network/36_basic.md)
- [ ] [37 | 套路篇：怎么评估系统的网络性能？](performance_tuning/network/37_routine.md)
- [ ] [38 | 案例篇：解决 DNS 解析时快时慢问题](performance_tuning/network/38_case.md)
- [ ] [39 | 案例篇：用 tcpdump 和 Wireshark 分析网络流量](performance_tuning/network/39_case.md)
- [ ] [40 | 案例篇：缓解 DDoS 攻击带来的性能下降问题](performance_tuning/network/40_case.md)
- [ ] [41 | 案例篇：处理网络请求延迟变大](performance_tuning/network/41_case.md)
- [ ] [42 | 案例篇：优化 NAT 性能-上](performance_tuning/network/42_case.md)
- [ ] [43 | 案例篇：优化 NAT 性能-下](performance_tuning/network/43_case.md)
- [ ] [44 | 套路篇：网络性能优化的几个思路-上](performance_tuning/network/44_routine.md)
- [ ] [45 | 套路篇：网络性能优化的几个思路-下](performance_tuning/network/45_routine.md)
- [ ] [46 | 答疑篇：网络收发过程中的缓冲区位置](performance_tuning/network/46_QA.md)
- [ ] [47 | 书单推荐：网络原理和 Linux 内核实现](performance_tuning/network/47_explore.md)

## 综合实战 13讲

- [ ] [48 | 案例篇：为什么应用容器化后，启动慢了很多？](performance_tuning/integrate/48_case.md)
- [ ] [49 | 案例篇：解决服务器总是时不时丢包的问题-上](performance_tuning/integrate/49_case.md)
- [ ] [50 | 案例篇：解决服务器总是时不时丢包的问题-下](performance_tuning/integrate/50_case.md)
- [ ] [51 | 案例篇：处理内核线程 CPU 利用率太高](performance_tuning/integrate/51_case.md)
- [ ] [52 | 案例篇：如何使用动态追踪-上](performance_tuning/integrate/52_case.md)
- [ ] [53 | 案例篇：如何使用动态追踪-下](performance_tuning/integrate/53_case.md)
- [ ] [54 | 案例篇：分析处理服务吞吐量下降很厉害的问题](performance_tuning/integrate/54_case.md)
- [x] [55 | 套路篇：系统监控的综合思路](performance_tuning/integrate/55_routine.md)
- [x] [56 | 套路篇：应用监控的一般思路](performance_tuning/integrate/56_routine.md)
- [x] [57 | 套路篇：分析性能问题的一般步骤](performance_tuning/integrate/57_routine.md)
- [x] [58 | 套路篇：优化性能问题的一般方法](performance_tuning/integrate/58_routine.md)
- [x] [59 | 套路篇：Linux 性能工具速查](performance_tuning/integrate/59_routine.md)
- [ ] [60 | 答疑篇：容器冷启动的性能分析](performance_tuning/integrate/60_QA.md)

## 尾篇

- [x] [结束语 | 愿你攻克性能难关](performance_tuning/how_to/61_end.md)

## 探索延伸

- [x] [书单推荐：性能优化和Linux 系统原理](performance_tuning/disk_io/33_explore.md)
- [x] [书单推荐：网络原理和 Linux 内核实现](performance_tuning/network/47_explore.md)
- [ ] [Linux 性能分析工具整理](performance_tuning/explore/tool.md)
- [x] [Linux 性能分析资源汇总](performance_tuning/explore/resource.md)
