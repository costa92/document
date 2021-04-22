# mac 安装 golang

## 源码安装

[https://github.com/golang/go.git](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fgolang%2Fgo.git)  or [https://go.googlesource.com/go](https://links.jianshu.com/go?to=https%3A%2F%2Fgo.googlesource.com%2Fgo)

Go工具链使用golang写成, 因此需要先安装go语言编译器, 参考[Installing Go from source](https://links.jianshu.com/go?to=https%3A%2F%2Fgolang.org%2Fdoc%2Finstall%2Fsource)

cgo支持还需要安装C语言编译器如gcc, 否则不需要cgo支持则设置环境变量: `CGO_ENABLED=0`

```sh
git clone https://github.com/golang/go.git
cd src
./all.bash
```

## 配置

## 工具安装

