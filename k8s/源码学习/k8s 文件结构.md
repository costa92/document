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