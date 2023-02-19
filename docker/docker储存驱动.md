#  docekr 储存驱动 

​	

1. OverlayFS：OverlayFS 是一个联合文件系统，它使用 Linux 内核中的 OverlayFS 建立在一个现有的文件系统之上，从而创建一个只读的基础镜像和一个可读写的层。
2. Aufs：Aufs（Another Union File System）也是一个联合文件系统，它通过将文件系统的多个分支合并到一个单独的挂载点上，创建一个虚拟文件系统。
3. Btrfs：Btrfs 是一个先进的复制文件系统，它具有快照、克隆和 RAID 支持等功能。Docker 通过将容器层保存为 Btrfs 子卷，从而实现了基于 Btrfs 的存储驱动程序。
4. Device Mapper：Device Mapper 是 Linux 内核中的一个基础设备映射器，可以将多个物理存储设备合并成一个逻辑设备。Docker 可以使用 Device Mapper 来创建基于块的存储池。
5. VFS：VFS（Virtual File System）是一种基于文件的储存驱动，它通过将容器层保存为文件来实现。

注意，不同版本的 Docker 支持的储存驱动可能略有不同。此外，不同的操作系统和文件系统支持的储存驱动也可能有所不同。



 1. overlay2: 对于所有当前支持的Linux 发行版， overlay2 是首选的储存驱动程序，不需要任何额外的配置
 2. aufs: 在内核3.13上的 ubuntu 14.04 上运行时，aufs 是 docker 18.06 及更早的版本的首选储存驱动程序，因为内核不支持 overlay2

 3. devicemapper: 虽然支持 devicemapper，但是在生产环境中需要 direct-lvm ，因为 loopback lvm 零配置时性能很差。devicemapper 是 CentOS 和 RHEL 的推荐存储驱动程序，在它们的内核版本不支持 overlay2 的时候。但是，当前版本的 CentOS 和 RHEL 现在支持 overlay2，现在是推荐的驱动程序。



