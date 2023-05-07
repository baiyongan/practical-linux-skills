# 基础篇：怎么理解内存中的Buffer和Cache？

!!! note
    内存和 CPU 的关系非常紧密，而内存管理本身也是很复杂的机制，所以感觉知识很硬核、很难啃，都是正常的。

    初学时不用非得理解所有内容，继续往后学，多理解相关的概念并配合一定的实践之后，再回头复习往往会容易不少。当然，基本功不容放弃。

## free 数据的来源

```shell
# 注意不同版本的free输出可能会有所不同
$ free
              total        used        free      shared  buff/cache   available
Mem:        8169348      263524     6875352         668     1030472     7611064
Swap:             0           0           0
```

这个界面包含了物理内存Mem和交换分区Swap的具体使用情况，比如总内存、已用内存、缓存、可用内存等。其中缓存是 Buffer和Cache两部分的总和。

这里的大部分指标都比较容易理解，但 Buffer和 Cache可能不太好区分。从字面上来说，Buffer是缓冲区，而Cache是缓存，两者都是数据在内存中的临时存储。那么，这两种“临时存储”有什么区别吗？

!!! tips
    碰到看不明白的指标时该怎么办?

    不懂就去查手册。用 man 命令查询 free 的文档，就可以找到对应指标的详细说明。

```shell
buffers
              Memory used by kernel buffers (Buffers in /proc/meminfo)

       cache  Memory used by the page cache and slabs (Cached and SReclaimable in /proc/meminfo)

       buff/cache
              Sum of buffers and cache
```

- Buffers 是内核缓冲区用到的内存，对应的是 /proc/meminfo 中的 Buffers 值。
  
- Cache 是内核页缓存和Slab用到的内存，对应的是 /proc/meminfo 中的 Cached 与 SReclaimable 之和。

这里的说明告诉我们，这些数值都来自 /proc/meminfo，但更具体的 Buffers、Cached和SReclaimable 的含义，还是没有说清楚。

!!! warning
    估计你第一反应就是去百度或者 Google一下。要注意，网上的结论可能是对的，但是很可能跟你的环境并不匹配。最简单来说，同一个指标的具体含义，就可能因为内核版本、性能工具版本的不同而有挺大差别。这也是为什么，专栏中强调通用思路和方法，而不是死记结论。对于案例实践来说，机器环境就是我们的最大限制。

## proc 文件系统

/proc 是 Linux 内核提供的一种特殊文件系统，是用户跟内核交互的接口。比方说，用户可以从 /proc 中查询内核的运行状态和配置选项，查询进程的运行状态、统计数据等，当然，你也可以通过 /proc 来修改内核的配置。proc 文件系统同时也是很多性能工具的最终数据来源。比如我们刚才看到的 free ，就是通过读取 /proc/meminfo ，得到内存的使用情况。

执行 man proc ，就可以得到 proc 文件系统的详细文档。

```shell
Buffers %lu
    Relatively temporary storage for raw disk blocks that shouldn't get tremendously large (20MB or so).

Cached %lu
   In-memory cache for files read from the disk (the page cache).  Doesn't include SwapCached.
...
SReclaimable %lu (since Linux 2.6.19)
    Part of Slab, that might be reclaimed, such as caches.
    
SUnreclaim %lu (since Linux 2.6.19)
    Part of Slab, that cannot be reclaimed on memory pressure.

```

- Buffers 是对原始磁盘块的临时存储，也就是**用来缓存磁盘的数据**，通常不会特别大（20MB左右）。这样，内核就可以把分散的写集中起来，统一优化磁盘的写入，比如可以把多次小的写合并成单次大的写等等。
  
- Cached 是从磁盘读取文件的页缓存，也就是**用来缓存从文件读取的数据**。这样，下次访问这些文件数据时，就可以直接从内存中快速获取，而不需要再次访问缓慢的磁盘。
  
- SReclaimable 是 Slab 的一部分。Slab包括两部分，其中的可回收部分，用 SReclaimable 记录；而不可回收部分，用 SUnreclaim 记录。

## 测试案例

### 场景一：磁盘和文件写案例

### 场景二：磁盘和文件读案例

## 小结

Buffer 和 Cache 分别缓存磁盘和文件系统的读写数据。

- 从写的角度，不仅可以优化磁盘和文件的写入，对应用程序也有好处，应用程序可以在数据真正落盘前，就返回去做其他工作。
- 从读的角度，既可以加速读取哪些需要频繁访问的数据，也降低了频繁 I/O 对磁盘的压力。


