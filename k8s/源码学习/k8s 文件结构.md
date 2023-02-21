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

k8s.io/kubernetes/plugin/
.
├── cmd
│   └── kube-scheduler          // kube-scheduler command的相关代码
│       ├── app                 // kube-scheduler app的启动
│       │   ├── options         
│       │   │   └── options.go  // 封装SchedulerServer对象和AddFlags方法
│       │   └── server.go       // 定义SchedulerServer的config封装和Run方法
│       └── scheduler.go        // kube-scheduler main方法入口
└── pkg
    ├── scheduler               // scheduler后端核心代码
    │   ├── algorithm
    │   │   ├── doc.go
    │   │   ├── listers.go      // 定义NodeLister和PodLister等Interface
    │   │   ├── predicates      // 定义kubernetes自带的Predicates Policies的Function实现
    │   │   │   ├── error.go
    │   │   │   ├── metadata.go
    │   │   │   ├── predicates.go   // 自带Predicates Policies的主要实现
    │   │   │   ├── predicates_test.go
    │   │   │   ├── utils.go
    │   │   │   └── utils_test.go
    │   │   ├── priorities      // 定义kubernetes自带的Priorities Policies的Function实现
    │   │   │   ├── balanced_resource_allocation.go    // defaultProvider - BalancedResourceAllocation
    │   │   │   ├── balanced_resource_allocation_test.go
    │   │   │   ├── image_locality.go    // defaultProvider - ImageLocalityPriority
    │   │   │   ├── image_locality_test.go
    │   │   │   ├── interpod_affinity.go   // defaultProvider - InterPodAffinityPriority
    │   │   │   ├── interpod_affinity_test.go
    │   │   │   ├── least_requested.go  // defaultProvider - LeastRequestedPriority
    │   │   │   ├── least_requested_test.go 
    │   │   │   ├── metadata.go         // priorityMetadata定义
    │   │   │   ├── most_requested.go   // defaultProvider - MostRequestedPriority
    │   │   │   ├── most_requested_test.go
    │   │   │   ├── node_affinity.go    // defaultProvider - NodeAffinityPriority
    │   │   │   ├── node_affinity_test.go
    │   │   │   ├── node_label.go       // 当policy.Argument.LabelPreference != nil时，会注册该Policy
    │   │   │   ├── node_label_test.go
    │   │   │   ├── node_prefer_avoid_pods.go  // defaultProvider - NodePreferAvoidPodsPriority 
    │   │   │   ├── node_prefer_avoid_pods_test.go
    │   │   │   ├── selector_spreading.go     // defaultProvider - SelectorSpreadPriority
    │   │   │   ├── selector_spreading_test.go
    │   │   │   ├── taint_toleration.go      // defaultProvider - TaintTolerationPriority
    │   │   │   ├── taint_toleration_test.go
    │   │   │   ├── test_util.go
    │   │   │   └── util                // 工具类
    │   │   │       ├── non_zero.go
    │   │   │       ├── topologies.go
    │   │   │       └── util.go
    │   │   ├── scheduler_interface.go    // 定义SchedulerExtender和ScheduleAlgorithm Interface
    │   │   ├── scheduler_interface_test.go
    │   │   └── types.go               // 定义了Predicates和Priorities Algorithm要实现的方法类型(FitPredicate, PriorityMapFunction)
    │   ├── algorithmprovider          // algorithm-provider参数配置的项
    │   │   ├── defaults    
    │   │   │   ├── compatibility_test.go
    │   │   │   └── defaults.go         // "DefaultProvider"的实现
    │   │   ├── plugins.go            // 空，预留自定义
    │   │   └── plugins_test.go
    │   ├── api                       // 定义Scheduelr API接口和对象，用于SchedulerExtender处理来自HTTPExtender的请求。
    │   │   ├── latest
    │   │   │   └── latest.go
    │   │   ├── register.go
    │   │   ├── types.go              // 定义Policy, PredicatePolicy,PriorityPolicy等
    │   │   ├── v1
    │   │   │   ├── register.go
    │   │   │   └── types.go
    │   │   └── validation
    │   │       ├── validation.go    // 验证Policy的定义是否合法
    │   │       └── validation_test.go
    │   ├── equivalence_cache.go    // 
    │   ├── extender.go               // 定义HTTPExtender的新建以及对应的Filter和Prioritize方法来干预预选和优选
    │   ├── extender_test.go
    │   ├── factory                    // 根据配置的Policies注册和匹配到对应的预选(FitPredicateFactory)和优选(PriorityFunctionFactory2)函数
    │   │   ├── factory.go             // 核心是定义ConfigFactory来工具配置完成scheduler的封装函数，最关键的CreateFromConfig和CreateFromKeys
    │   │   ├── factory_test.go
    │   │   ├── plugins.go             // 核心是定义注册自定义预选和优选Policy的方法
    │   │   └── plugins_test.go
    │   ├── generic_scheduler.go        // 定义genericScheduler，其Schedule(...)方法作为调度执行的真正开始的地方
    │   ├── generic_scheduler_test.go
    │   ├── metrics                    // 支持注册metrics到Prometheus
    │   │   └── metrics.go
    │   ├── scheduler.go                // 定义Scheduler及Run()，核心的scheduleOne()方法也在此，scheduleOne()一个完成的调度流程，包括或许待调度Pod、调度、Bind等
    │   ├── scheduler_test.go
    │   ├── schedulercache       
    │   │   ├── cache.go               // 定义schedulerCache对Pod，Node，以及Bind的CURD，以及超时维护等工作
    │   │   ├── cache_test.go
    │   │   ├── interface.go           // schedulerCache要实现的Interface
    │   │   ├── node_info.go          // 定义NodeInfo及其相关Opertation
    │   │   └── util.go
    │   └── testing
    │       ├── fake_cache.go
    │       └── pods_to_cache.go
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
