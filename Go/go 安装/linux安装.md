linux  安装 golang

下载

```sh
~ ➤ wget https://go.dev/dl/go1.18.darwin-arm64.tar.gz
wget https://go.dev/dl/go1.22.2.linux-amd64.tar.gz
```


 解压

```sh
sudo tar -C /usr/local -xzf go1.18.linux-amd64.tar.gz go/
sudo tar -C /usr/local -xzf go1.22.2.linux-amd64.tar.gz go/
// 解压到当前目录
~ ➤ tar -zxvf go1.18.linux-amd64.tar.gz
```

修改环境变量：

```sh
vim /etc/profile

## 文件最后添加
export PATH=/usr/local/nodejs/bin:/usr/local/go/bin:$PATH
## 生效
source /etc/profile
```

设置 go 的环境变量

```sh
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
source ~/.bashrc

```

