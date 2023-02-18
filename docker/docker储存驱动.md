#  docekr 储存驱动

 1. overlay2: 对于所有当前支持的Linux 发行版， overlay2 是首选的储存驱动程序，不需要任何额外的配置
 2. aufs: 在内核3.13上的 ubuntu 14.04 上运行时，aufs 是 docker 18.06 及更早的版本的首选储存驱动程序，因为内核不支持 overlay2

 3. devicemapper: 虽然支持 devicemapper，但是在生产环境中需要 direct-lvm ，因为 loopback lvm 零配置时性能很差。devicemapper 是 CentOS 和 RHEL 的推荐存储驱动程序，在它们的内核版本不支持 overlay2 的时候。但是，当前版本的 CentOS 和 RHEL 现在支持 overlay2，现在是推荐的驱动程序。



