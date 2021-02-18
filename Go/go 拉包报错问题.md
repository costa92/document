# go 拉包报错问题

当我们使用 `go get`、`go install`、`go mod` 等命令时，会自动下载相应的包或依赖包。但由于众所周知的原因，类似于 `golang.org/x/...` 的包会出现下载失败的情况。如下所示：

```bash
$ go get -u golang.org/x/sys 

go get golang.org/x/sys: unrecognized import path "golang.org/x/sys"  (https fetch: Get https://golang.org/x/sys?go-get=1: dial tcp 216.239.37.1:443: i/o timeout)
```

## 解决方式

我们常见的 `golang.org/x/...` 包，一般在 GitHub 上都有官方的镜像仓库对应。比如 `golang.org/x/text` 对应 `github.com/golang/text`。所以，我们可以手动下载或 clone 对应的 GitHub 仓库到指定的目录下。

```bash
mkdir $GOPATH/src/golang.org/x cd  $GOPATH/src/golang.org/x git clone git@github.com:golang/text.git rm -rf text/.git
```

当如果需要指定版本的时候，该方法就无解了，因为 GitHub 上的镜像仓库多数都没有 tag。并且，手动嘛，程序员怎么能干呢，尤其是依赖的依赖，太多了。

### 设置代理

如果你**有代理**，那么可以设置对应的环境变量：

```bash
export http_proxy=http://proxyAddress:port
export https_proxy=http://proxyAddress:port
```
或者，直接用 `all_proxy`：

```sh
export all_proxy=http://proxyAddress:port
```

 ### go mod replace

从 Go 1.11 版本开始，新增支持了 `go modules` 用于解决包依赖管理问题。该工具提供了 `replace`，就是为了解决包的别名问题，也能替我们解决 `golang.org/x` 无法下载的的问题。

`go module` 被集成到原生的 `go mod` 命令中，但是如果你的代码库在 `$GOPATH` 中，`module` 功能是默认不会开启的，想要开启也非常简单，通过一个环境变量即可开启 `export GO111MODULE=on`。

以下为参考示例：
```fallback
module example.com/hello

require (
    golang.org/x/text v0.3.0
)

replace (
    golang.org/x/text => github.com/golang/text v0.3.0
)

```

类似的还有 [glide](https://github.com/Masterminds/glide)、[gopm](https://github.com/gpmgo/gopm) 等这类第三方包管理工具，都不同方式的解决方案提供给我们。


###  GOPROXY 环境变量

终于到了本文的终极大杀器 —— **GOPROXY**。

我们知道从 `Go 1.11` 版本开始，官方支持了 `go module` 包依赖管理工具。

其实还新增了 `GOPROXY` 环境变量。如果设置了该变量，下载源代码时将会通过这个环境变量设置的代理地址，而不再是以前的直接从代码库下载。这无疑对我等无法科学上网的开发良民来说是最大的福音。

更可喜的是，[goproxy.io](https://github.com/goproxyio/goproxy) 这个开源项目帮我们实现好了我们想要的。该项目允许开发者一键构建自己的 `GOPROXY` 代理服务。同时，也提供了公用的代理服务 `https://goproxy.io`，我们只需设置该环境变量即可正常下载被墙的源码包了： 

```sh
export GOPROXY=https://goproxy.io
```

 |
不过，**需要依赖于 `go module` 功能**。可通过 `export GO111MODULE=on` 开启 MODULE。

如果项目不在 `GOPATH` 中，则无法使用 `go get ...`，但可以使用 `go mod ...` 相关命令。

也可以通过置空这个环境变量来关闭，`export GOPROXY=`。

对于 Windows 用户，可以在 `PowerShell` 中设置：

```sh
$env:GOPROXY = "https://goproxy.io"
```

或者
```sh
go env -w GOPROXY = "https://goproxy.io"
```


最后，我们当然推荐使用 `GOPROXY` 这个环境变量的解决方式，前提是 **Go version >= 1.11**。

最后的最后，七牛也出了个国内代理 [goproxy.cn](https://github.com/goproxy/goproxy.cn) 方便国内用户更快的访问不能访问的包，真是良心。

## 参考资料

*   [goproxy.io for Go modules](https://mp.weixin.qq.com/s/COethtOaiygsYev-kkCc4A)
*   [goproxy.io](https://goproxy.io/)
*   [goproxy.cn](https://github.com/goproxy/goproxy.cn)
