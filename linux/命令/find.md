# find 

### 查看所有超过800M大小的文件的文件名称

```bash
find . -type f -size +800M
```

### 查找超过800M大小文件，并显示更详细的文件信息

```bash
find . -type f -size +800M  -print0 | xargs -0 ls -l
```

### 当需要查找超过800M大小文件，并显示查找出来文件的具体大小，可以使用下面命令

```bash
find . -type f -size +800M  -print0 | xargs -0 du -h
```

### 当需要对查找结果按照文件大小做一个排序，那么可以使用下面命令

```bash
find . -type f -size +800M  -print0 | xargs -0 du -h | sort -nr
```