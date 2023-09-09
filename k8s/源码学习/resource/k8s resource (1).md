#  k8s resource

kubernetes 是基于 API 的 infrastructure，在此之上的 kubernetes 之中的概念都被抽象成各种 resource

不同的 resource 拥有不同的功能，经常使用的 resource 有：

1. Pod:  k8s 最小的部署单元，可以包一个或多个容器。
2. Deployment: 用于管理Pod的创建和更新。 
3. Service:  用于管理一组Pod 的访问方式，可以通过IP地址或DNS名进行访问。
4. ConfigMap:  用于保存应用程序的配置信息，可以被Pod中的容器引用。
5. Secret： 用于保存敏感的信息，如密码、秘钥，可以被Pod中的容器引用
6. Volume： 用于将数据持久化到磁盘上，例如：可以将Pod中的容器的日志输出到Volume中
7. Namespace: 用于将 k8s 集群划分为多个虚拟集群，以方便不同的团队或应用程序可以彼此隔离。
8. Node: 集群中的一个物理或虚拟机器，可以运行一个或多个Pod
9. StatefulSet: 用于管理有状态应用程序的部署，如：数据库
10. DaemonSet: 用于在所有或部分节点上运行守护程序
11. Job/CronJob: 用于管理一次性任务或定时任务
12. Horizontal Pod Autoscaler: 用于根据Pod的CPU 使用率自动缩放Pod的数量

还有其他资源等等。

 k8s 对各种 resource 的操作基于 API 来完成，k8s 提供一系列的 RESTFull API 来完成对 resource 的基本操作，k8s API是一个RESTful AP，可以使用多种编程语言和工具来进行访问和管理。Kubernetes提供了一组API对象和RESTful API接口，允许用户以编程方式访问和控制Kubernetes集群中的各种资源。

对于Kubernetes中的资源来说，基本上有两个维度的划分：

1. 基于Namespace的维度：Namespace是Kubernetes中一种虚拟的集群概念，用于将集群中的资源划分为不同的逻辑组。同一种资源在不同的Namespace中可以拥有不同的定义和使用，从而实现对资源的分组管理和隔离。

2. 基于是否为核心资源的维度：核心资源（Core Resources）是Kubernetes中最基本和最重要的资源，包括了Kubernetes集群中的核心组件以及用于部署和管理应用程序的基本资源对象。除了核心资源外，Kubernetes还有很多扩展资源，它们都是在核心资源的基础上进行扩展的，用于提供更多高级功能和能力。

   

resource 基本上有两个维度的划分， 一个是基于 **计算资源** 的维度，还有一个是**持久化存储资源**

1. **计算资源（Compute Resources）**：计算资源包括CPU和内存。在Kubernetes中，计算资源的配置对于保证Pod或容器的稳定性和可用性至关重要。为了避免资源争夺和资源饥饿的问题，Kubernetes可以基于计算资源为Pod或容器分配所需的CPU和内存资源，并控制它们的使用。通过资源限制（Resource Limits）和资源请求（Resource Requests）来控制计算资源的使用。
2. **持久化存储资源（Persistent Storage Resources）**：持久化存储资源包括存储卷（Volume）、持久卷（Persistent Volume）和持久卷声明（Persistent Volume Claim）。在Kubernetes中，这些资源用于提供持久性的存储，以便应用程序可以在容器和节点之间移动时仍然可以访问数据。Kubernetes中的存储资源可以是本地存储、网络存储或云存储等。



Kubernetes的API是通过kube-apiserver组件暴露出来的，它是Kubernetes中的一个核心组件，负责管理所有API请求和响应。通过API，用户可以使用各种客户端（如kubectl、Kubernetes Dashboard、自定义脚本等）对集群中的资源进行管理和操作。

Kubernetes API的设计遵循了RESTful API的标准，并且使用了标准的HTTP方法（如GET、POST、PUT、DELETE等）和URI路径（如/api/v1/namespaces、/api/v1/pods等）来表示不同的资源和操作。API请求和响应都是通过JSON或YAML格式的数据进行交互。

使用Kubernetes API，用户可以完成以下操作：

- 创建、修改和删除各种资源对象，如Pod、Service、Deployment、ConfigMap等。
- 查询和获取各种资源对象的信息和状态，如获取Pod的状态、获取Service的IP地址等。
- 执行各种操作，如重新部署应用、扩容应用、更新应用配置等。
- 监控集群和资源对象的状态，如获取CPU、内存使用情况、监控日志等。

Kubernetes API的使用非常灵活，用户可以根据自己的需要编写自己的客户端程序或脚本来对集群中的资源进行管理和操作。同时，Kubernetes API还提供了丰富的权限控制和安全机制，确保只有授权的用户才能进行操作，并且保护集群的安全和稳定。