# cgroup 技术

cgroup 全称是 controller group， 它是用来做”控制“的，控制资源的使用，

cgroup 定义了下面的一系列子系统，每个子系统用于控制某一个资源

*  CPU 子系统，主要限制进程的CPU使用率。
*  cpuacct 子系统，可以统计cgroup中的进程的CPU使用报告。
*  cpuset 子系统，可以为cgroup中的进程分配单独的CPU节点或者内存节点。
*  memory 子系统， 可以限制进程的 Memory 使用量
*  blkio 子系统，可以限制进程的块设备 IO
*  devices 子系统，可以控制进程能够访问某些设备。
*  net_cls 子系统，可以标记 cgroups 中进程的网络数据包，然而可以使用tc模块(traffic control) 对数据包进行控制。
*  freezer 子系统，可以挂起或恢复 cgroup 中的进程。

查看 cgroup 文件内容：
```sh
$ cat /proc/cgroups
#subsys_name	hierarchy	num_cgroups	enabled
cpuset	6	1	1
cpu	7	3	1
cpuacct	7	3	1
memory	2	2	1
devices	5	1	1
freezer	9	1	1
net_cls	3	1	1
blkio	8	1	1
perf_event	11	1	1
hugetlb	4	1	1
pids	10	1	1
net_prio	3	1	1
```

在 Linux 上，为了操作 cgroup，有一个专门的 cgroup 文件系统， 运行 mount 命令可以查看
```sh
$ mount -t cgroup
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/usr/lib/systemd/systemd-cgroups-agent,name=systemd)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_prio,net_cls)
cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpuacct,cpu)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
```

cgroup 文件系统多挂载到 /sys/fs/cgroup下，通过上面命令行，查看 cgroup 控制的资源

```sh
$ cd /sys/fs/cgroup/ && ll -a
drwxr-xr-x 13 root root 340 12月 15 2019 .
drwxr-xr-x  6 root root   0 1月  28 2021 ..
drwxr-xr-x  2 root root   0 12月 15 2019 blkio
lrwxrwxrwx  1 root root  11 12月 15 2019 cpu -> cpu,cpuacct
lrwxrwxrwx  1 root root  11 12月 15 2019 cpuacct -> cpu,cpuacct
drwxr-xr-x  4 root root   0 5月   6 07:42 cpu,cpuacct
drwxr-xr-x  2 root root   0 12月 15 2019 cpuset
drwxr-xr-x  2 root root   0 12月 15 2019 devices
drwxr-xr-x  2 root root   0 12月 15 2019 freezer
drwxr-xr-x  2 root root   0 12月 15 2019 hugetlb
drwxr-xr-x  3 root root   0 5月   6 07:42 memory
lrwxrwxrwx  1 root root  16 12月 15 2019 net_cls -> net_cls,net_prio
drwxr-xr-x  2 root root   0 12月 15 2019 net_cls,net_prio
lrwxrwxrwx  1 root root  16 12月 15 2019 net_prio -> net_cls,net_prio
drwxr-xr-x  2 root root   0 12月 15 2019 perf_event
drwxr-xr-x  2 root root   0 12月 15 2019 pids
drwxr-xr-x  4 root root   0 12月 15 2019 systemd

```