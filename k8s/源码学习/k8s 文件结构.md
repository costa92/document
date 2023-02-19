# k8s 项目的文件结构

```yaml
api: 输出接口文档用，基本是json源码
build：构建脚本
cmd：所有的二进制可执行文件入口代码，也就是各种命令的接口代码。
pkg：项目diamante主目录，cmd只是接口，这里是具体实现。cmd类似业务代码，pkg类似核心
plugin：插件
test：测试相关的工具
third_party：第三方工具
docs：文档
example：使用例子
Godeps：项目依赖的Go的第三方包，比如docker客户端sdk，rest等
hack：工具箱，各种编译，构建，校验的脚本都在这。

/cmd/kube-scheduler
├── app
│ ├── BUILD
│ ├── server.go //schedule初始化以及运行启动函数
├── BUILD
├── OWNERS
├── scheduler.go //schedule main函数
/pkg
├── plugin/pkg/admission
├── plugin/pkg/auth //相关认证
├── plugin/pkg/scheduler //schedule主要逻辑，包含预选优选算法、测量等
├── plugin/pkg/scheduler/algorithm //schedule预选优选算法
├── ...
├── plugin/pkg/scheduler/schedulercache //schedule缓存，便于业务逻辑
├── plugin/pkg/scheduler/metrics// 测量相关
├── BUILD
├── testutil.go
├── OWNERS
├── scheduler.go // scheduler的代码逻辑入口，其中scheduleOne函数就在里面
├── scheduler_test.go
```


参考:https://github.com/daniel-hutao/k8s-source-code-analysis



```sh
Kubernetes (k8s) 的源代码包括许多不同的组件和模块，这些组件和模块分布在不同的目录中。以下是 Kubernetes 源代码的主要目录结构：

api/：定义 Kubernetes API 的 protobuf 文件和代码生成的 Go 代码
cmd/：包含启动和管理 Kubernetes 组件的命令行工具
pkg/：包含 Kubernetes 代码库的核心代码
apiserver/：定义 Kubernetes API 服务器的代码
controller/：定义 Kubernetes 控制器的代码
kubelet/：定义 Kubernetes kubelet 的代码
scheduler/：定义 Kubernetes 调度器的代码
client/：定义 Kubernetes 客户端的代码
apis/：包含 Kubernetes API 的不同版本的定义和代码
apis/apps/：包含 Kubernetes 应用程序 API 的定义和代码
apis/extensions/：包含 Kubernetes 扩展 API 的定义和代码
apis/core/：包含 Kubernetes 核心 API 的定义和代码
vendor/：包含所有依赖项的代码，其中每个依赖项都被拷贝到其自己的子目录中

除此之外，还有其他一些目录和文件，如 test/ 用于单元测试和集成测试代码， docs/ 用于文档等。总的来说，Kubernetes 源代码的目录结构非常清晰，易于理解和使用。
```

