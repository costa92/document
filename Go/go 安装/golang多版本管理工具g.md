# golang多版本管理工具g

## golang多版本管理工具g
g是一个Linux、macOS、Windows下的命令行工具，可以提供一个便捷的多版本go环境的管理和切换

特性：

- 支持列出可供安装的go版本号
- 支持列出已安装的go版本号
- 支持在本地安装多个go版本
- 支持卸载已安装的go版本
- 支持在已安装的go版本之间自由切换

## 安装

#### 自动化安装

```
# 建议安装前清空`GOROOT`、`GOBIN`等环境变量
$ wget -qO- https://raw.githubusercontent.com/voidint/g/master/install.sh | bash
$ echo "unalias g" >> ~/.bashrc # 可选。若其他程序（如'git'）使用了'g'作为别名。
$ source ~/.bashrc # 或者 source ~/.zshrc
```

#### 手动安装

- 下载对应平台的[二进制压缩包(github)](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvoidint%2Fg%2Freleases)。
- 将压缩包解压至`PATH`环境变量目录下，如`/usr/local/bin`。
- 编辑shell环境配置文件（`~/.bashrc`、`~/.zshrc`...）

```
$ cat>>~/.bashrc<<EOF　　export GOROOT="${HOME}/.g/go"
export PATH="${HOME}/.g/go/bin:$PATH"
export G_MIRROR=https://golang.google.cn/dl/
EOF
```

# g的使用

#### g的帮助文档

```sh
# g --help
NAME:
  g - Golang Version Manager

 USAGE:
  g  command [arguments...]

 VERSION:
  1.5.0

 AUTHOR:
  voidint <voidint@126.com>

 COMMANDS:
    ls         List installed versions
    ls-remote  List remote versions available for install
    use        Switch to specified version
    install    Download and install a version
    uninstall  Uninstall a version
    clean      Remove files from the package download directory
    self       Modify g itself
    help, h    Shows a list of commands or help for one command

 GLOBAL OPTIONS:
  --help, -h     show help (default: false)
  --version, -v  print the version (default: false)
```

#### 使用当前可供安装的stable状态的go版本

```sh
# g ls-remote stable
  1.19.9
  1.20.4
```

#### 安装指定版本的go

```sh
# 安装go1.16.15
go install 1.16.15

# 安装go1.18.10
go install 1.18.10

# 安装go1.17.13
go install 1.17.13
# go version
go version go1.17.13 linux/amd64
```

切换go

```sh
# g use 1.18.10
go version go1.18.10 linux/amd64
```

### 技术问答

- 环境变量**`G_MIRROR`**有什么作用？

  由于中国大陆无法自由访问Golang官网，导致查询及下载go版本都变得困难，因此可以通过该环境变量指定一个镜像站点（如**`https://golang.google.cn/dl/`**），g将从该站点查询、下载可用的go版本。

  