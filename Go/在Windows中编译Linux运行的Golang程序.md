# 在Windows中编译Linux运行的Golang程序

打开CMD，先修改Go环境参数，然后再编译。编译结束恢复为windows的环境参数。

注意：不知道为什么，在VsCode的Terminal中操作时会失败，但是在cmd.exe中是可以的。

### 第一步，修改go环境参数

```bash
go env -w CGO_ENABLED=0
go env -w GOOS=linux
go env -w GOARCH=amd64
```
设置完之后，可以查看一下设置是否生效：
```bash
go env CGO_ENABLED
go env GOOS
go env GOARCH
```
### 第二步，编译
环境参数设置为linux编译时的参数后，即可正常编译：
```bash
go build main.go
```
### 第三步，将环境参数改回windows
也可不改回，取决于具体需要
```bash
go env -w CGO_ENABLED=1
go env -w GOOS=windows
go env -w GOARCH=amd64
```
