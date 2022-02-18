# golangci-lint 静态检查代码



使用教程官网文档：[golangci-lint](https://golangci-lint.run/usage/linters/)

**golangci lint**

能快速执行linters。并行运行linter，使用缓存，支持yaml配置，与所有主要IDE集成，并包含数十个linter，支持定制化。



支持配置的格式（非必需，可以自己定义配置，没有会默认）

- .golangci.yml
- .golangci.yaml
- .golangci.toml
- .golangci.json

GolangCI Lint还搜索从第一个分析路径的目录到根目录的所有目录中的配置文件。如果没有找到配置文件，GolangCI Lint将尝试在主目录中找到一个。要查看正在使用哪个配置文件以及从何处获取配置文件，请使用-v选项运行查看



使用：

默认使用的9种linter

deadcode *# 未使用的代码*

errcheck *#* 是否对error处理

gosimple #检查代码是否可以简化

govet # 检查 go 源代码并报告可疑结构，例如 Printf 调用，其参数与格式字符串不一致

ineffassign #检测是否有未使用的代码、变量、常量、类型、结构体、函数、函数参数等

staticcheck #静态分析检查

structcheck #检查没有用的结构体字段

unused #检查未使用的常量，变量，函数和类型

varcheck #查找未使用的全局变量和常量