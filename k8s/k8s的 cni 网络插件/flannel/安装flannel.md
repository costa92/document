# flannel 安装

一、下载文件

```sh
wget https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

二、修改配置文件

有三个参数需要指定：

```sh
1. --network-plugin   *# 调用的网络插件。如： --network-plugin=cni。*
2. --cni-bin-dir      *# cni插件目录*
3. --cni-conf-dir     *# cni配置目录，kubelet通过这个配置确定使用什么cni插件。然后在--cni-bin-dir目录里查找需要的插件。*
```



```sh
vim kube-flannel.yml
```

1 .替换镜像

```sh
142:        image: docker.io/flannel/flannel-cni-plugin:v1.1.2
143:       #image: docker.io/rancher/mirrored-flannelcni-flannel-cni-plugin:v1.1.2
154:        image: docker.io/flannel/flannel:v0.21.5
155:       #image: docker.io/rancher/mirrored-flannelcni-flannel:v0.21.5
169:        image: docker.io/flannel/flannel:v0.21.5
170:       #image: docker.io/rancher/mirrored-flannelcni-flannel:v0.21.5
```

2. 修改  配置文件中的 Network 为 集群的 pod network CIDR

```yaml
  "Network": "10.244.0.0/16",  
```



如何获取到 集群的  pod network CIDR

1. 从集群中每个节点获取 pod CIDR 地址。

```sh
 kubectl get nodes -o jsonpath='{.items[*].spec.podCIDR}'
```

  输出：

```sh
10.244.0.0/24
```



2. kube-proxy所使用的 pod网络CIDR

```sh  
kubectl cluster-info dump | grep -m 1 cluster-cidr
```

   输出：

```
  "--cluster-cidr=10.244.0.0/16",
```

3.  `--cluster-cidr` / `--pod-network-cidr`反馈给kube-controller-manager的配置。

```sh
ps -ef | grep "cluster-cidr"
```

```sh
kube-controller-manager --allocate-node-cidrs=true --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf --bind-address=127.0.0.1 --client-ca-file=/etc/kubernetes/pki/ca.crt --cluster-cidr=10.244.0.0/16 --cluster-name=kubernetes --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt --cluster-signing-key-file=/etc/kubernetes/pki/ca.key --controllers=*,bootstrapsigner,tokencleaner --kubeconfig=/etc/kubernetes/controller-manager.conf --leader-elect=true --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --root-ca-file=/etc/kubernetes/pki/ca.crt --service-account-private-key-file=/etc/kubernetes/pki/sa.key --service-cluster-ip-range=10.96.0.0/12 --use-service-account-credentials=true
```

4. 在文件 `/etc/kubernetes/manifests/kube-controller-manager.yaml` 中的

   ```sh
   # sudo grep cidr /etc/kubernetes/manifests/kube-*
   /etc/kubernetes/manifests/kube-controller-manager.yaml:    - --allocate-node-cidrs=true
   /etc/kubernetes/manifests/kube-controller-manager.yaml:    - --cluster-cidr=192.168.0.0/16
   /etc/kubernetes/manifests/kube-controller-manager.yaml:    - --node-cidr-mask-size=24
   ```

   

三、部署

```sh
kubectl apply -f kube-flannel.yml
```

四、检查

看一下/var/run/flannel/subnet.env文件：

```sh
cat /var/run/flannel/subnet.env
FLANNEL_NETWORK=10.244.0.0/16
FLANNEL_SUBNET=10.5.0.1/24
FLANNEL_MTU=1500
FLANNEL_IPMASQ=true
```



参考：https://www.yxingxing.net/archives/kubernetes-20200307-flannel-cni#5%E4%BF%AE%E6%94%B9kubelet%E5%90%AF%E5%8A%A8%E5%8F%82%E6%95%B0
