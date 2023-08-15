# istion



## Istion 环境安装

下载最新版本

```sh
wget https://storage.googleapis.com/istio-release/releases/1.15.6/istio-1.15.6-linux-amd64.tar.gz
tar zxvf istio-1.15.6-linux-amd64.tar.gz -C /usr/local/
```

查看版本

```sh
istioctl version
```

## 使用 istioctl 的安装方式

 demo 这个 profile 进行安装

```sh
istioctl install --set profile=demo -y
```

查看istio相应的 namespace 和 pod 是否已经正常创建：

```sh	
kubectl get ns |grep istio
istio-system      Active   18m
```

查看 pods

```sh
kubectl get pods -n istio-system
```

```sh
NAME                                    READY   STATUS    RESTARTS   AGE
istio-egressgateway-865c97d8bb-42l4m    1/1     Running   0          14m
istio-ingressgateway-7465654cf6-x9tqf   1/1     Running   0          14m
istiod-74c8fcd4b4-bnmb7                 1/1     Running   0          9m22s
```

检查 istio 的 CRD 和 API 资源：

```sh
kubectl get crd |grep istio
```

```sh
authorizationpolicies.security.istio.io    2023-07-28T06:15:12Z
destinationrules.networking.istio.io       2023-07-28T06:15:12Z
envoyfilters.networking.istio.io           2023-07-28T06:15:12Z
gateways.networking.istio.io               2023-07-28T06:15:12Z
istiooperators.install.istio.io            2023-07-28T06:15:12Z
peerauthentications.security.istio.io      2023-07-28T06:15:12Z
proxyconfigs.networking.istio.io           2023-07-28T06:15:12Z
requestauthentications.security.istio.io   2023-07-28T06:15:12Z
serviceentries.networking.istio.io         2023-07-28T06:15:12Z
sidecars.networking.istio.io               2023-07-28T06:15:12Z
telemetries.telemetry.istio.io             2023-07-28T06:15:12Z
virtualservices.networking.istio.io        2023-07-28T06:15:12Z
wasmplugins.extensions.istio.io            2023-07-28T06:15:12Z
workloadentries.networking.istio.io        2023-07-28T06:15:12Z
workloadgroups.networking.istio.io         2023-07-28T06:15:12Z
```

```sh
kubectl api-resources |grep istio
```

```sh
wasmplugins                                    extensions.istio.io/v1alpha1           true         WasmPlugin
istiooperators                    iop,io       install.istio.io/v1alpha1              true         IstioOperator
destinationrules                  dr           networking.istio.io/v1beta1            true         DestinationRule
envoyfilters                                   networking.istio.io/v1alpha3           true         EnvoyFilter
gateways                          gw           networking.istio.io/v1beta1            true         Gateway
proxyconfigs                                   networking.istio.io/v1beta1            true         ProxyConfig
serviceentries                    se           networking.istio.io/v1beta1            true         ServiceEntry
sidecars                                       networking.istio.io/v1beta1            true         Sidecar
virtualservices                   vs           networking.istio.io/v1beta1            true         VirtualService
workloadentries                   we           networking.istio.io/v1beta1            true         WorkloadEntry
workloadgroups                    wg           networking.istio.io/v1beta1            true         WorkloadGroup
authorizationpolicies                          security.istio.io/v1beta1              true         AuthorizationPolicy
peerauthentications               pa           security.istio.io/v1beta1              true         PeerAuthentication
requestauthentications            ra           security.istio.io/v1beta1              true         RequestAuthentication
telemetries                       telemetry    telemetry.istio.io/v1alpha1            true         Telemetry
```

## 问题

1. 出现: Istio 常见问题 - configmap istio-ca-root-cert not found

```sh
MountVolume.SetUp failed for volume "istiod-ca-cert" : configmap "istio-ca-root-cert" not found
```

解决:

````sh
kubectl rollout restart deployment istiod -n istio-system
````



add namspace label 

```sh
kubectl label namespace default istio-injection=enabled
```

