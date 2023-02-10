Envoy:  Istio 的数据平面

Istiod 为 istio 的控制平面， 提供服务发现



Pilot： 为 Envoy Sidecar 提供服务发现功能，为智能路由

Citadel： 通内置身份和凭证管理可以提供加强的服务与服务之间的最终用户身份验证（不好用） 可以用于升级服务网络中未加密的流量

Galley: 负责配置管理的组件，用于验证配置信息的格式和正确性， Galley使用网络配置协议（Mesh Configuration Protocol）和其他组件进行配置交互。



## 东西流量管理   VirtualService （VS）

VirtualService:  VirtualService (虚拟服务) 基于 Istio 和对应平台提供的基本连通性和服务发现服务能力，将请求路由到对应的目标。 每个VirtualService 包含一个组路由规则。lstio 将每个请求根据路由配置到指定的目标地址

南北流量管理 Gateway

Gateway: Istio 网关功能，可以使用 Gateway在网络最外层接收HTTP/TCP 流量，并将流量转发到网格内的某一个服务，同时支持出口流量的管控，可以将出口的流量固定从 ergressGateway 的 服务中代理出去

