# Mac m1 golang debug  模式

1. 下载最新的版本 dlv

```bash
git clone https://github.com/go-delve/delve.git
```

2. 进行编译安装 
```bash
cd delve/cmd/dlv
go build
go install
```

3. 查看安装

```bash
echo $GOPATH
cd $GOPATH/bin && ls
```
4. golang ide配置

* **第一种方法**: 复制 dlv 文件到 ide 的配置文件下
```bash
 cp dlv /Applications/GoLand.app/Contents/plugins/go/lib/dlv/Mac
```

* **第二种方式**：修改ide文件的配置文件

打开Goland中点击: Help → Edit Custom Properties...
在文件添加下面代码:

```bash
dlv.path=/usr/local/bin/dlv
```
注意：/usr/local/bin 是 按照dlv的位置，如果一般默认安装的位置是在 $GOPATH/bin,或者是在go安装的目录下

5. 重启glang，进行测试debug模式

