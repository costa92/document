# go mod

1. 任意目录创建项目目录

```bash
 mkdir /home/mygo
```

2. 进入到项目目录
```bash
 cd  /home/mygo
```

3. 初始化生成`go.mod` 文件
```bash
go mod  init mygo
```
 注意：这里mygo名字也可以叫其他名字，一般为了与项目名称对应，就用项目名字

4. 项目目录下会生成go.mod文件， 类似于python的requirements.txt文件。同时也生成一个go.sum文件，主要记载了下载包的哈希值用于校验，我们用不到。

5. go.mod文件一旦创建后，它的内容将会被go toolchain全面掌控。
 go toolchain 会在各类命令执行时，比如执行 go get、go build、go run、go mod等命令时，自动修改和维护go.mod文件，这点跟pip还是有区别的

6. go.mod 提供了`module`, `require`、`replace`和`exclude` 四个命令

*   `  module` 语句指定包的名字（路径）
*   `  require` 语句指定的依赖项模块
*   `  replace` 语句可以替换依赖项模块
*   `  exclude` 语句可以忽略依赖项模块

```go
module mygo

go 1.15

require (
   github.com/dgrijalva/jwt-go v3.2.0+incompatible
   github.com/tal-tech/go-zero v1.1.4
   golang.org/x/crypto v0.0.0-20200622213623-75b288015ac9
   gorm.io/driver/mysql v1.0.3
   gorm.io/gorm v1.20.11
)
```

7. 可以使用命令 `go list -m -u all` 来检查可以升级的package，使用`go get -u need-upgrade-package` 升级后会将新的依赖版本更新到go.mod文件中。也可以使用 `go get -u` 升级所有依赖。
8.  由于某些已知的原因，并不是所有的package都能成功下载，比如：`golang.org`下的包。

可以在 go.mod 文件中使用 replace 指令替换成github上对应的库，来下载相应的包。比如：

replace (

```go
replace (
    golang.org/x/crypto v0.0.0-20190701094942-4def268fd1a4 => github.com/golang/cryptov0.0.0-20190701094942-4def268fd1a4 )或者： replace golang.org/x/crypto v0.0.0-20190701094942-4def268fd1a4 => github.com/golang/crypto v0.0.0-20190701094942-4def268fd1a4
```
