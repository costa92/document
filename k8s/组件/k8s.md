# 开发接口



k8s 设计理念与分布系统：

分析和理解 k8s 的设计理念可以使我们更加深入地了解 k8s 系统，更好地利用它管理分布式部署的云原生应用，另一方也可以让我们借鉴其在分布式系统设计方面的经验。

## 分层架构

K8s 设计理念和功能其实就是一个类似 Linux的分层架构

![Kubernetes 分层架构示意图](http://img.longqiuhong.com/picgo/img/k8s_layer.jpg)

- **核心层**:  kubernertes 最核心的功能，对外提供 API 构建高层的应用，对内提供插件式应用执行环境
- **应用层**:  部署( 无状态应用、有状态应用、批处理任务、集群应用等) 和 路由 (服务发现、DNS解析等)
- **管理层**： 系统度量（如基础设施、容器和网络的度量），自动化（如自动化扩展、动态Provision等）以及策略管理(RBAC、Quoa、PSP、NetworkPolicy)
- **接口层**:  kubekle 命令行工具、客户端SDK以及集群联邦
- **生态系统**: 在接口层之上的庞大容器集群管理调度的生态系统，可以划分为两个范畴
- **Kubernetes外部**：日志、监控、配置管理、CI、CD、Workflow、FaaS、OTS应用、ChatOps等
- **Kubernetes内部** ：CRI 、CNI 、CSI、镜像仓库、Cloud Provider、集群自身的配置和管理等

## 设计理解

​	![Kubernetes 设计理念](http://img.longqiuhong.com/picgo/img/kubernetes-design.png)

这里将按照顺序分别介绍声明式、显式接口、无侵入性和可移植性这几个设计理念。

**声明式**：

声明式(Declarative) 的编程方式一直都会被工程师们拿来与命令式（Imperative）进行对比,这两者是完成不同的编程方法。

**声明式VS命令式**

> 命令式编程（Imperative）：详细的命令机器怎么（How）去处理一件事情以达到想要的结果（What）；
>
> 声明式编程（Declarative）： 只告诉你想要的结果 （What），机器自己搜索过程（How）

声明式的方式能够大量减少使用工作量、极大地增加开发的效率，这是因为声明式本能够简化需要的代码，减少开发人员的工作，如果使用命令式的方式进行开发，虽然在配置上比较灵活，但是带来了更多的工作

k8s 中的 YAML 文件也有着相同的原理，用户可以告诉 k8s 想要的最终状态是什么，而它会帮助从现有的状态进行迁移

**显式接口**

 k8s 的接口设计规范是不存在内部的私有接口，所有的接口都是显示定义的，组件之间通信使用的接口对于使用者来说都是显式的，直接可以调用使用

**无侵入性**

为了满足用户的需求，减少工作量与任务并增强灵活性，k8s 为用户提供无侵入式的接入方式 ，每一个应用或服务一旦被打包成镜像就可以直接在k8s中 无缝对接使用，不需要更改应用程序中的任务代码

![Non-Invasive](http://img.longqiuhong.com/picgo/img/kuberentes-non-invasive.png)

Docker 和 Kubernetes 就像包裹在应用程序上的两层，它们两个为应用程序提供了容器化以及编排的能力，在应用程序内部却不需要任何的修改就能够在 Docker 和 Kubernetes 集群中运行，这是 Kubernetes 在设计时选择无侵入带来最大的好处，同时无侵入的接入方式也是目前几乎所有应用程序或者服务都必须考虑的一点。

**可移植性**

在微服务架构中，我们往往都会让所有处理业务的服务变成无状态的服务，以前在内存中存储的数据、Session 等缓存，现在都会放到 Redis、ETCD 等数据库中存储，微服务架构要求我们对业务进行拆分并划清服务之间的边界，所以有状态的服务往往会对架构的水平迁移带来障碍。

然而有状态的服务其实是无可避免的，我们将每一个基础服务或者业务服务都变成了一个个只负责计算的进程，但是仍然需要有其他的进程负责存储易失的缓存和持久的数据，Kubernetes 对这种有状态的服务也提供了比较好的支持。

Kubernetes 引入了 PersistentVolume 和 PersistentVolumeClaim 的概念用来屏蔽底层存储的差异性，目前的 Kubernetes 支持下列类型的 PersistentVolume：

![Persistent](http://github.com/bmwx4/k8s-in-practice/raw/master/images/persistent.png)

这些不同的 PersistentVolume 会被用户声明的 PersistentVolumeClaim 分配到不同的服务中，对于上层来讲所有的服务都不需要感知 PersistentVolume，只需要直接使用 PersistentVolumeClaim 得到的卷就可以了。

1. 容器运行时接口（CRI）
2. 容器网络接口 (CNI)
3. 容器储存接口（CSI）





参考文档：

1. [文档](https://github.com/bmwx4/k8s-in-practice)
2. [Pod init](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/init-containers/) 