#  Ubuntu  多版本安装golang

## 一、 安装 golang

安装命令：

```sh
sudo apt install golang-go
```

查看是否安装成功

```sh
go version


```
查看环境变量


```sh
go env
```

修改环境变量
```sh
echo "export GO111MODULE=on" >> ~/.profile
echo "export GOPROXY=https://goproxy.cn" >> ~/.profile
source ~/.profile
```

## 使用包装器安装

下载包装器，并生成 golang 的可执行文件

```sh
go install golang.org/dl/go1.22.0@latest
go1.22.0 download
```

验证安装

```sh
go1.22.0  version
```

替换环境变量

```sh
export GOROOT=$(go1.22.0 env GOROOT) 
export PATH=${GOROOT}/bin:$PATH 
```

设置 gobin的环境变量

```sh
if [ -d "$HOME/go/bin" ] ; then
   export GOBIN="$HOME/go/bin"
   export PATH=$GOBIN:$PATH
fi
```



