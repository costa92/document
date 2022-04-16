# mac 多个版本安装

Mac 下使用 homebrew 可以轻松实现 Go 多版本切换。

使用以下方法安装最新版本：

```sh
brew install go
```

查看当前版本：

```sh
>> go version
go version go1.17.3 darwin/arm64
```

使用以下方法安装指定版本：

```sh
 brew install go@1.18
```

如果不知道能安装哪些版本可以通过下面的命令查询

```sh
brew search go
// brew search go@1
```

如果需要安装最新版本没有的话，需要更新

```sh
brew update
```

首先unlink，删除关联的 golang

```sh
brew unlink go
```

指定关联版本
```sh
 brew link go@1.18
```

查看版本
```sh
go version
go version go1.18.1 darwin/arm64
```