# k8s集群使用calico遇到的问题

1、“Readiness probe failed: caliconode is not ready: BIRD is not ready: BGP not established with”

查看主机网络信息

```
ifconfig
calidd44b079cb1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1440
        inet6 fe80::ecee:eeff:feee:eeee  prefixlen 64  scopeid 0x20<link>
        ether ee:ee:ee:ee:ee:ee  txqueuelen 0  (Ethernet)
        RX packets 399164506  bytes 55063970580 (51.2 GiB)
        RX errors 0  dropped 10492980  overruns 0  frame 0
        TX packets 403817609  bytes 77027774608 (71.7 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:57:89:0d:a4  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.11.102  netmask 255.255.255.0  broadcast 192.168.11.255
        inet6 fe80::5054:ff:fe60:5120  prefixlen 64  scopeid 0x20<link>
        ether 52:54:00:60:51:20  txqueuelen 1000  (Ethernet)
        RX packets 399164506  bytes 55063970580 (51.2 GiB)
        RX errors 0  dropped 10492980  overruns 0  frame 0
        TX packets 403817609  bytes 77027774608 (71.7 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 300147382  bytes 95712847390 (89.1 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 300147382  bytes 95712847390 (89.1 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

tunl0: flags=193<UP,RUNNING,NOARP>  mtu 1440
        inet 172.28.82.192  netmask 255.255.255.255
        tunnel   txqueuelen 1000  (IPIP Tunnel)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```



修改 calico.yaml 文件添加以下二行

```
 - name: IP_AUTODETECTION_METHOD
   value: "interface=eth.*" # ens 根据实际网卡开头配置，支持正则表达式
```

重新应用calico.yaml

```
kubectl apply -f calico.yaml
```

查看

```
kubectl get pod --all-namespaces
```



2、Unable to authenticate the request due to an error: [invalid bearer token, square/go-jose: error in cryptographic primitive

```
kubectl delete secret  -n kube-system coredns-token-6n887

```



3、[ERROR][8] startup/startup.go 146: failed to query kubeadm’s config map error=Get https://10.10.0.1:443/api/v1/namespaces/kube-system/configmaps/kubeadm-config?timeout=2s: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)

环境：

Kubernetes master 与 node 节点分别在不同云厂商



问题原因：

Node工作节点连接不到 `apiserver` 地址，检查一下calico配置文件，要把apiserver的IP和端口配置上，如果不配置的话，calico默认将设置默认的calico网段和443端口。字段名：`KUBERNETES_SERVICE_HOST`、`KUBERNETES_SERVICE_PORT`、`KUBERNETES_SERVICE_PORT_HTTPS`。



修改 calico.yaml 文件

```
- name: KUBERNETES_SERVICE_HOST
  value: "kube-apiserver"  # master apiserver 地址
- name: KUBERNETES_SERVICE_PORT
  value: "6443"
- name: KUBERNETES_SERVICE_PORT_HTTPS
  value: "6443"
```

