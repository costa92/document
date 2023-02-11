VirtualService 配置

```yaml
apiVersion: networking.istio.io/v1beta1    => 对应的API的版本
kind: VirtualService  

```



DestinationRule: destination (目标规则) 用于后端真实的服务再做进一步

根据 Pod 的 label 信息的来处理

1. 具有 app=paycenter 和 version=v1的标签的 pod，归为 v1 版本

2. 具有 app=paycenter 和 version=v2的标签的 pod，归为 v1 版本



