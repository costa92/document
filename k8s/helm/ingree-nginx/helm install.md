# ingree-nginx 安装

安装地址：

https://artifacthub.io/packages/helm/ingress-nginx/ingress-nginx

### helm repo 

```sh
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

### helm pull 

```helm
# 搜索版本
helm search repo ingress-nginx

# 查看可用的版本
helm search repo ingress-nginx --versions

#拉取源码
helm pull ingress-nginx/ingress-nginx 

# 拉取指定版本
helm pull ingress-nginx/ingress-nginx --version=4.0.10

```

### 安装

1. 解压

```sh
tar -zxvf ingress-nginx-4.0.10.tgz -C /temp/
cd /temp/
```
2. 修改 value.yaml
因为国内无法访问国外地址需要修改

修改镜像地址： 
修改 controller 镜像地址
  
```yaml
controller:
  name: controller
  image:
    registry: k8s.gcr.io
    image: nginx-ingress/controller
    # for backwards compatibility consider setting the full image url via the repository value below
    # use *either* current default registry/image or repository format or installing chart by providing the values.yaml will fail
    # repository:
    tag: "v1.0.0"
    pullPolicy: IfNotPresent
    # www-data -> uid 101
    runAsUser: 101
    allowPrivilegeEscalation: true
``` 

修改成

```yaml
controller:
  name: controller
  image:
    registry: registry.cn-beijing.aliyuncs.com
    image: dotbalo/controller
    # for backwards compatibility consider setting the full image url via the repository value below
    # use *either* current default registry/image or repository format or installing chart by providing the values.yaml will fail
    # repository:
    tag: "v1.0.0"
    pullPolicy: IfNotPresent
    # www-data -> uid 101
    runAsUser: 101
    allowPrivilegeEscalation: true
    
```

修改镜像 kube-webhook-certgen 地址

```yaml
    patch:
      enabled: true
      image:
        registry: k8s.gcr.io
        image: nginx-ingress/kube-webhook-certgen
        # for backwards compatibility consider setting the full image url via the repository value below
        # use *either* current default registry/image or repository format or installing chart by providing the values.yaml will fail
        # repository:
        tag: v1.0
        pullPolicy: IfNotPresent
      ## Provide a priority class name to the webhook patching job
      ##
      priorityClassName: ""
      podAnnotations: {}
      nodeSelector:
        kubernetes.io/os: linux
      tolerations: []
      runAsUser: 2000
```

修改成

```yaml

    patch:
      enabled: true
      image:
        registry: registry.cn-beijing.aliyuncs.com
        image: dotbalo/kube-webhook-certgen
        # for backwards compatibility consider setting the full image url via the repository value below
        # use *either* current default registry/image or repository format or installing chart by providing the values.yaml will fail
        # repository:
        tag: v1.0
        pullPolicy: IfNotPresent
      ## Provide a priority class name to the webhook patching job
      ##
      priorityClassName: ""
      podAnnotations: {}
      nodeSelector:
        kubernetes.io/os: linux
      tolerations: []
      runAsUser: 2000
```

修改 Pod’s DNS Policy

dnsPolicy 与 hostNetwork 两个字段

```yaml

  # Optionally change this to ClusterFirstWithHostNet in case you have 'hostNetwork: true'.
  # By default, while using host network, name resolution uses the host's DNS. If you wish nginx-controller
  # to keep resolving names inside the k8s network, use ClusterFirstWithHostNet.
  dnsPolicy: ClusterFirst

  # Bare-metal considerations via the host network https://kubernetes.github.io/ingress-nginx/deploy/baremetal/#via-the-host-network
  # Ingress status was blank because there is no Service exposing the NGINX Ingress controller in a configuration using the host network, the default --publish-service flag used in standard cloud setups does not apply
  reportNodeInternalIp: false

  # Process Ingress objects without ingressClass annotation/ingressClassName field
  # Overrides value for --watch-ingress-without-class flag of the controller binary
  # Defaults to false
  watchIngressWithoutClass: false

  # Required for use with CNI based kubernetes installations (such as ones set up by kubeadm),
  # since CNI and hostport don't mix yet. Can be deprecated once https://github.com/kubernetes/kubernetes/issues/23920
  # is merged
  hostNetwork: false
```
改成

```yaml
  # Optionally change this to ClusterFirstWithHostNet in case you have 'hostNetwork: true'.
  # By default, while using host network, name resolution uses the host's DNS. If you wish nginx-controller
  # to keep resolving names inside the k8s network, use ClusterFirstWithHostNet.
  dnsPolicy: ClusterFirstWithHostNet

  # Bare-metal considerations via the host network https://kubernetes.github.io/ingress-nginx/deploy/baremetal/#via-the-host-network
  # Ingress status was blank because there is no Service exposing the NGINX Ingress controller in a configuration using the host network, the default --publish-service flag used in standard cloud setups does not apply
  reportNodeInternalIp: false

  # Process Ingress objects without ingressClass annotation/ingressClassName field
  # Overrides value for --watch-ingress-without-class flag of the controller binary
  # Defaults to false
  watchIngressWithoutClass: false

  # Required for use with CNI based kubernetes installations (such as ones set up by kubeadm),
  # since CNI and hostport don't mix yet. Can be deprecated once https://github.com/kubernetes/kubernetes/issues/23920
  # is merged
  hostNetwork: true

```

更改控制器类型为DaemoSet：

```yaml
  ## Additional environment variables to set
  extraEnvs: []
  # extraEnvs:
  #   - name: FOO
  #     valueFrom:
  #       secretKeyRef:
  #         key: FOO
  #         name: secret-resource

  ## DaemonSet or Deployment
  ##
  kind: Deployment

```
修改成

```yaml
  ## Additional environment variables to set
  extraEnvs: []
  # extraEnvs:
  #   - name: FOO
  #     valueFrom:
  #       secretKeyRef:
  #         key: FOO
  #         name: secret-resource

  ## DaemonSet or Deployment
  ##
  kind: DaemonSet
```


指定节点选择规则：

```yaml

  ## Node labels for controller pod assignment
  ## Ref: https://kubernetes.io/docs/user-guide/node-selection/
  ##
  nodeSelector:
    kubernetes.io/os: linux
```    
修改
```yaml

  ## Node labels for controller pod assignment
  ## Ref: https://kubernetes.io/docs/user-guide/node-selection/
  ##
  nodeSelector:
    kubernetes.io/os: linux
    ingress: "true"
```    

3. 给节点添加标签

```sh
kubectl get node
# 添加标签
kubectl label node k8s-node5 ingress=nginx
# 查看标签
kubectl get node --show-labels
```

4. 安装 

```sh
## 创建一个空间
kubectl create ns ingress-nginx
## 安装
helm upgrade --install ingress-nginx -n ingress-nginx .
```

5. 删除

```sh
helm delete ingress-nginx -n ingress-nginx 
kubectl  delete job -n ingress-nginx --all
```