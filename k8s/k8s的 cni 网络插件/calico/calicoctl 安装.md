#  calicoctl 安装

### 一、下载和安装calicoctl工具

**注意calico版本与calicoctl版本要相同**

[GitHub](https://github.com/projectcalico/calicoctl/releases)

```
cd /usr/local/bin
curl -O -L https://github.com/projectcalico/calicoctl/releases/download/v3.11.3/calicoctl
chmod +x calicoctl
```



### 二 、编辑配置文件/etc/calico/calicoctl.cfg

如果使用集群内部的etcd集群,使用如下配置：

```
cat > /etc/calico/calicoctl.cfg << EOF
apiVersion: projectcalico.org/v3
kind: CalicoAPIConfig
metadata:
spec:
  datastoreType: "kubernetes"
  kubeconfig: "/root/.kube/config"
EOF
```



如果使用外面的etcd

```
apiVersion: projectcalico.org/v3
kind: CalicoAPIConfig
metadata:
spec:
  datastoreType: etcdv3
  etcdEndpoints: https://192.168.11.101:2379,https://192.168.11.130:2379,https://192.168.11.134:2379
  etcdKeyFile: /etc/kubernetes/pki/etcd/etcd-key.pem
  etcdCertFile: /etc/kubernetes/pki/etcd/etcd.pem
  etcdCACertFile: /etc/kubernetes/pki/etcd/etcd-ca.pem

```

### 三、calicoctl常用命令

查看帮助命令：

calicoctl -h

```
Usage:
  calicoctl [options] <command> [<args>...]

    create       Create a resource by filename or stdin.
    replace      Replace a resource by filename or stdin.
    apply        Apply a resource by filename or stdin.  This creates a resource
                 if it does not exist, and replaces a resource if it does exists.
    patch        Patch a pre-exisiting resource in place.
    delete       Delete a resource identified by file, stdin or resource type and
                 name.
    get          Get a resource identified by file, stdin or resource type and
                 name.
    label        Add or update labels of resources.
    convert      Convert config files between different API versions.
    ipam         IP address management.
    node         Calico node management.
    version      Display the version of calicoctl.
    export       Export the Calico datastore objects for migration
    import       Import the Calico datastore objects for migration
    datastore    Calico datastore management.

Options:
  -h --help               Show this screen.
  -l --log-level=<level>  Set the log level (one of panic, fatal, error,
                          warn, info, debug) [default: panic]

Description:
  The calicoctl command line tool is used to manage Calico network and security
  policy, to view and manage endpoint configuration, and to manage a Calico
  node instance.

  See 'calicoctl <command> --help' to read about a specific subcommand.
```

查看网络节点：

```
# calicoctl get node

NAME         
k8s-master   
k8s-node1    
k8s-node4    
k8s-node5    
```

查看网络状态：

```
# calicoctl node status 

Calico process is running.

IPv4 BGP status
+----------------+-------------------+-------+----------+-------------+
|  PEER ADDRESS  |     PEER TYPE     | STATE |  SINCE   |    INFO     |
+----------------+-------------------+-------+----------+-------------+
| 192.168.11.129 | node-to-node mesh | up    | 08:10:28 | Established |
| 192.168.11.132 | node-to-node mesh | up    | 08:10:29 | Established |
| 192.168.11.133 | node-to-node mesh | up    | 08:10:29 | Established |
+----------------+-------------------+-------+----------+-------------+

IPv6 BGP status
No IPv6 peers found.

```



STATE 的值为 “up”为正常，状态为“start”为还在启动状态，未就绪



### 四、使用calicoctl工具来对calico进行更改

​	查看问题节点的yaml文件

```
calicoctl get node k8s-node1 -o yaml

```

结果：

```
apiVersion: projectcalico.org/v3
kind: Node
metadata:
  annotations:
    projectcalico.org/kube-labels: '{"beta.kubernetes.io/arch":"amd64","beta.kubernetes.io/os":"linux","kubernetes.io/arch":"amd64","kubernetes.io/hostname":"k8s-node1","kubernetes.io/os":"linux","node.kubernetes.io/node":""}'
  creationTimestamp: "2021-03-25T06:01:40Z"
  labels:
    beta.kubernetes.io/arch: amd64
    beta.kubernetes.io/os: linux
    kubernetes.io/arch: amd64
    kubernetes.io/hostname: k8s-node1
    kubernetes.io/os: linux
    node.kubernetes.io/node: ""
  name: k8s-node1
  resourceVersion: "282099"
  uid: 20bbcb92-8d68-4caa-a2e0-2e8ff6d20d78
spec:
  bgp:
    ipv4Address: 192.168.11.129/24
    ipv4IPIPTunnelAddr: 172.31.156.64
  orchRefs:
  - nodeName: k8s-node1
    orchestrator: k8s
status: {}

```

如果 ipv4Address 错误的时候 修改为 k8s-node1.yaml 的内容

```
calicoctl get node k8s-node1 -o yaml > k8s-node1.yaml
```

```
apiVersion: projectcalico.org/v3
kind: Node
metadata:
  annotations:
    projectcalico.org/kube-labels: '{"beta.kubernetes.io/arch":"amd64","beta.kubernetes.io/os":"linux","kubernetes.io/arch":"amd64","kubernetes.io/hostname":"k8s-node1","kubernetes.io/os":"linux","node.kubernetes.io/node":""}'
  creationTimestamp: "2021-03-25T06:01:40Z"
  labels:
    beta.kubernetes.io/arch: amd64
    beta.kubernetes.io/os: linux
    kubernetes.io/arch: amd64
    kubernetes.io/hostname: k8s-node1
    kubernetes.io/os: linux
    node.kubernetes.io/node: ""
  name: k8s-node1
  resourceVersion: "282099"
  uid: 20bbcb92-8d68-4caa-a2e0-2e8ff6d20d78
spec:
  bgp:
    ipv4Address: 192.168.11.129/24  # 修改为 192.168.11.130
    ipv4IPIPTunnelAddr: 172.31.156.64
  orchRefs:
  - nodeName: k8s-node1
    orchestrator: k8s
status: {}
```



重启：

```
calicoctl apply -f k8s-node1.yaml
kubectl get pod -n kube-system
```

