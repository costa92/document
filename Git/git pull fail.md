# close 失败

## 1.  git clone 报错fetch-pack: unexpected disconnect while reading sideband packet

### 报错信息如下
```sh
fetch-pack: unexpected disconnect while reading sideband packet
```

### 方法一
```sh
首先关闭 core.compression
git config --global core.compression 0

然后使用depth来下载最近一次提交
git clone --depth 1 <url地址>

使用这个命令来获取所有的分支信息（）
git fetch --unshallow

最后pull一下查看状态，问题解决
git pull --all
```

### 方法二
```sh
//增加缓存空间大小
git config --global http.postBuffer 524288000
//一个意思也是提升大小
git config --global core.packedGitLimit 512m 
git config --global core.packedGitWindowSize 512m 
git config --global pack.deltaCacheSize 2047m 
git config --global pack.packSizeLimit 2047m 
git config --global pack.windowMemory 2047m 
git config --global http.lowSpeedLimit 0
git config --global http.lowSpeedTime  999999
```


### 检测 rsa 使用的情况
```sh
ssh -T -ai ~/.ssh/id_rsa git@github.com
```
