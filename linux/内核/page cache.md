![img](https://static001.geekbang.org/resource/image/f3/1b/f344917f3cacd5bc06ae7c743a217f1b.png?wh=2860*2440)

很明显，Page Cache 是内核管理的内存，也就是说，它属于内核不属于用户。

查看 page cache 有如下方法：

1. /proc/meminfo
2. free 
3. /proc/vmstat

根据 `/proc/meminfo` 方法来查询

```sh
$ cat /proc/meninfo
```

```sh
...
Buffers:            1224 kB
Cached:           111472 kB
SwapCached:        36364 kB
Active:          6224232 kB
Inactive:         979432 kB
Active(anon):    6173036 kB
Inactive(anon):   927932 kB
Active(file):      51196 kB
Inactive(file):    51500 kB
...
Shmem:             10000 kB
...
SReclaimable:      43532 kB
...
```

计数公式为：

> Buffers + Cached + SwapCached = Active(file) + Inactive(file) + Shmem + SwapCached
>
> ```bash
> Buffers + Cached + SwapCached = Active(file) + Inactive(file) + Shmem + SwapCached
>   * 等式右边这些项目把Buffers和Cached细分了，分成了Active(file)、Inactive(file)和Shmem
>   * Buffers更加依赖于内核实现，在不同内核版本中含义可能不一致
>   * 等式右边和应用程序的关系更加直接
> 
> Active(file)+Inactive(file)
> 	* File-backed page（与文件对应的内存页）
> 	* mmap()内存映射方式和buffered I/O来消耗的内存就属于这部分
> 	* 实际生产环境最容易产生问题
> 
> SwapCached
> 	* 是打开了swap后，把Inactive(anon)+Active(anon)这两项里匿名页（比如进程的堆、栈等，没有一个文件作为back store）给交换磁盘(swap out)，然后再读入到内存(swap in)后分配的内存
> 	* 读入到内存后原来的Swap File还在，所以SwapCached也可以认为是File-bcached page，即属于Page Cache
> 	* 目的：减少I/O
> 	* SwapCached产生过程
> 		![1-1.利用数据观测Page Cache_page cache_02](https://s2.51cto.com/images/blog/202009/17/1cf614cb45453dc5fcb9ea624f5c1995.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)
> 		* SwapCache只在Swap分区打开情况下才会有，Swap过程产生的I/O会很容易引起抖动
> 
> Shmem
> 	* 是指匿名共享映射这种方式分配的内存（free命令中的shared这一项）
> 	* 比如：tmpfs（临时文件系统）
> 	* 此部分生产环境中问题比较少
> 
> ```

计数为：

```sh
298428 + 4250460 + 0 = 2283068 + 2254012 + 2348 + 0

=> 4548888 = 4539428
```

[/PROC/MEMINFO之谜](https://gist.github.com/baymaxium/f4131c180d1af2e24bb36bf4f5ab5b05)



两边等式都是 Page Cache，即：

> Page Cache = Buffers + Cached + SwapCached



Free 

free -k

通过 procfs 源码里面的proc/sysinfo.c这个文件，你可以发现 buff/cache 包括下面这几项：

> buff/cache = Buffers + Cached + SReclaimable



Page Cache 是如何“诞生”的？

Page Cache 的产生有两种不同的方式：

​	1. Buffered I/O（标准 I/O）；

	2. Memory-Mapped I/O（存储映射 I/O）。



观测

```sh
$ cat /proc/vmstat | egrep "dirty|writeback"
nr_dirty 40
nr_writeback 2
```

如上所示，nr_dirty 表示当前系统中积压了多少脏页，nr_writeback 则表示有多少脏页正在回写到磁盘中，他们两个的单位都是 Page(4KB)。

Page Cache 是如何“死亡”的？

​	你可以把 Page Cache 的回收行为 (Page Reclaim) 理解为 Page Cache 的“自然死亡”。



![img](https://static001.geekbang.org/resource/image/1d/bb/1d430c93e397e23d67d12e28827c4bbb.jpg?wh=3658*2138)

回收的方式主要是两种：直接回收和后台回收。



观察 Page Cache 直接回收和后台回收最简单方便的方式是使用 sar：

```sh
$ sar -B 1
02:14:01 PM  pgpgin/s pgpgout/s   fault/s  majflt/s  pgfree/s pgscank/s pgscand/s pgsteal/s    %vmeff


02:14:01 PM      0.14    841.53 106745.40      0.00  41936.13      0.00      0.00      0.00      0.00
02:15:01 PM      5.84    840.97  86713.56      0.00  43612.15    717.81      0.00    717.66     99.98
02:16:01 PM     95.02    816.53 100707.84      0.13  46525.81   3557.90      0.00   3556.14     99.95
02:17:01 PM     10.56    901.38 122726.31      0.27  54936.13   8791.40      0.00   8790.17     99.99
02:18:01 PM    108.14    306.69  96519.75      1.15  67410.50  14315.98     31.48  14319.38     99.80
02:19:01 PM      5.97    489.67  88026.03      0.18  48526.07   1061.53      0.00   1061.42     99.99
```

借助上面这些指标，你可以更加明确地观察内存回收行为，下面是这些指标的具体含义：

pgscank/s : kswapd(后台回收线程) 每秒扫描的 page 个数。

pgscand/s: Application 在内存申请过程中每秒直接扫描的 page 个数。

pgsteal/s: 扫描的 page 中每秒被回收的个数。

%vmeff: pgsteal/(pgscank+pgscand), 回收效率，越接近 100 说明系统越安全，越接近 0 说明系统内存压力越大。



这几个指标也是通过解析 /proc/vmstat 里面的数据来得出的，对应关系如下：

![img](https://static001.geekbang.org/resource/image/46/aa/4604ec0da3f87ec003317fb3337fa9aa.jpg?wh=2619*1616)