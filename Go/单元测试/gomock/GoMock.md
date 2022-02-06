# GoMock测试框架

GoMock是由Golang官方开发维护的测试框架，实现了较为完整的基于interface的Mock功能，能够与Golang内置的testing包良好集成，也能用于其它的测试环境中。GoMock测试框架包含了GoMock包和mockgen工具两部分，其中GoMock包完成对桩对象生命周期的管理，mockgen工具用来生成interface对应的Mock类源文件。
GoMock官网：

```bash
 https://github.com/golang/mock
```
GoMock安装：

```bash
go get github.com/golang/mock/gomock
```

mockgen辅助代码生成工具安装：

```bash
go get github.com/golang/mock/mockgen
```

GoMock文档：

```bash
go doc github.com/golang/mock/gomock
```

