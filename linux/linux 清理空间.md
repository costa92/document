# linux 清理空间

处理 linux 日志文件与 docker 未使用的 docker镜像

### 查看磁盘使用情况

* **df 命令**

查看各个文件系统大小和可用量：

```sh
df -ah
```
* **du 命令**

查看目录内文件大小，查找大文件：

```sh
du -sh *
```
du 的参数说明：

**-h**: 是代表human-readable，以 K、M、G为单位。

**-s**: 或者 --summarize 表示仅显示总计。

### 日志文件清理

通过查找大文件，发现 /var/log/journal/

```sh
cd /var/log/journal/c28d40cbc8e3adcb4e32d9779a77b39e
ls -lhm --full-time . | sort -k6 | head
# 可以看到最旧的几条日志的日期时间和大小以及总的日志大小
```
* 只保留一周的日志

```sh
journalctl --vacuum-time=1w
```

* 只保留10M的日志 

```sh
journalctl --vacuum-size=10M
```

### 清理 docker

* 查看 docker 磁盘使用

```sh
docker system df
```
* 清理无用的磁盘使用
会删除停止的容器、无用的数据卷和网络和dangling 镜像

```
docker system prune
```
加上 -a 会一并删除没有容器使用 Docker 镜像

```sh
docker system prune -a
```

* 查看卷
```sh
docker valume ls
```
* 清除没有容器正在使用数据卷
```sh
docker volume prune
```