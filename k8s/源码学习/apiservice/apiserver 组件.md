# apiservice 组件

kube-apiserver 共由 3 个组件构成（Aggregator、KubeAPIServer、APIExtensionServer），这些组件依次通过 Delegation 处理请求：

- **Aggregator**：暴露的功能类似于一个七层负载均衡，将来自用户的请求拦截转发给其他服务器，并且负责整个 APIServer 的 Discovery 功能；
- **KubeAPIServer** ：负责对请求的一些通用处理，认证、鉴权等，以及处理各个内建资源的 REST 服务；
- **APIExtensionServer**：主要处理 CustomResourceDefinition（CRD）和 CustomResource（CR）的 REST 请求，也是 Delegation 的最后一环，如果对应 CR 不能被处理的话则会返回 404。

Aggregator 和 APIExtensionsServer 对应两种主要扩展 APIServer 资源的方式，即分别是 AA 和 CRD。



## 启动流程

首先分析 kube-apiserver 的启动方式，kube-apiserver 也是通过其 `Run` 方法启动主逻辑的，在`Run` 方法调用之前会进行解析命令行参数、设置默认值等。





