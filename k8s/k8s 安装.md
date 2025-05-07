```
cfssl gencert -initca ca-csr.json | cfssljson -bare /etc/kubernetes/pki/ca
```



```
    cfssl gencert   -ca=/etc/kubernetes/pki/ca.pem   -ca-key=/etc/kubernetes/pki/ca-key.pem   -config=ca-config.json   -hostname=10.96.0.1,192.168.11.102,127.0.0.1,kubernetes,kubernetes.default,kubernetes.default.svc,kubernetes.default.svc.cluster,kubernetes.default.svc.cluster.local,192.168.11.129,192.168.11.132,192.168.11.133   -profile=kubernetes   apiserver-csr.json | cfssljson -bare /etc/kubernetes/pki/apiserver
    
```



```
cfssl gencert   -initca front-proxy-ca-csr.json | cfssljson -bare /etc/kubernetes/pki/front-proxy-ca 
```



```
cfssl gencert   -ca=/etc/kubernetes/pki/front-proxy-ca.pem   -ca-key=/etc/kubernetes/pki/front-proxy-ca-key.pem   -config=ca-config.json   -profile=kubernetes   front-proxy-client-csr.json | cfssljson -bare /etc/kubernetes/pki/front-proxy-client
```





```
cfssl gencert \
   -ca=/etc/kubernetes/pki/ca.pem \
   -ca-key=/etc/kubernetes/pki/ca-key.pem \
   -config=ca-config.json \
   -profile=kubernetes \
   manager-csr.json | cfssljson -bare /etc/kubernetes/pki/controller-manager
```

```
kubectl config set-cluster kubernetes \
     --certificate-authority=/etc/kubernetes/pki/ca.pem \
     --embed-certs=true \
     --server=https://192.168.11.102:6443 \
     --kubeconfig=/etc/kubernetes/controller-manager.kubeconfig
```



```
kubectl config set-context system:kube-controller-manager@kubernetes \
    --cluster=kubernetes \
    --user=system:kube-controller-manager \
    --kubeconfig=/etc/kubernetes/controller-manager.kubeconfig
```



```
kubectl config set-credentials system:kube-controller-manager \
     --client-certificate=/etc/kubernetes/pki/controller-manager.pem \
     --client-key=/etc/kubernetes/pki/controller-manager-key.pem \
     --embed-certs=true \
     --kubeconfig=/etc/kubernetes/controller-manager.kubeconfig
```



```
kubectl config use-context system:kube-controller-manager@kubernetes \
     --kubeconfig=/etc/kubernetes/controller-manager.kubeconfig
```

```
cfssl gencert \
   -ca=/etc/kubernetes/pki/ca.pem \
   -ca-key=/etc/kubernetes/pki/ca-key.pem \
   -config=ca-config.json \
   -profile=kubernetes \
   scheduler-csr.json | cfssljson -bare /etc/kubernetes/pki/scheduler
```



```
kubectl config set-cluster kubernetes \
     --certificate-authority=/etc/kubernetes/pki/ca.pem \
     --embed-certs=true \
     --server=https://192.168.11.102:6443 \
     --kubeconfig=/etc/kubernetes/scheduler.kubeconfig
```

```
kubectl config set-credentials system:kube-scheduler \
     --client-certificate=/etc/kubernetes/pki/scheduler.pem \
     --client-key=/etc/kubernetes/pki/scheduler-key.pem \
     --embed-certs=true \
     --kubeconfig=/etc/kubernetes/scheduler.kubeconfig
```



```
kubectl config set-context system:kube-scheduler@kubernetes \
     --cluster=kubernetes \
     --user=system:kube-scheduler \
     --kubeconfig=/etc/kubernetes/scheduler.kubeconfig
```



```
kubectl config use-context system:kube-scheduler@kubernetes \
     --kubeconfig=/etc/kubernetes/scheduler.kubeconfig
```

```
cfssl gencert \
   -ca=/etc/kubernetes/pki/ca.pem \
   -ca-key=/etc/kubernetes/pki/ca-key.pem \
   -config=ca-config.json \
   -profile=kubernetes \
   admin-csr.json | cfssljson -bare /etc/kubernetes/pki/admin
```

```
kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.11.102:6443     --kubeconfig=/etc/kubernetes/admin.kubeconfig
```

```
kubectl config set-credentials kubernetes-admin     --client-certificate=/etc/kubernetes/pki/admin.pem     --client-key=/etc/kubernetes/pki/admin-key.pem     --embed-certs=true     --kubeconfig=/etc/kubernetes/admin.kubeconfig
```

```
kubectl config set-context kubernetes-admin@kubernetes     --cluster=kubernetes     --user=kubernetes-admin     --kubeconfig=/etc/kubernetes/admin.kubeconfig
```

```
kubectl config use-context kubernetes-admin@kubernetes     --kubeconfig=/etc/kubernetes/admin.kubeconfig
```

```
openssl genrsa -out /etc/kubernetes/pki/sa.key 2048
```

```
openssl rsa -in /etc/kubernetes/pki/sa.key -pubout -out /etc/kubernetes/pki/sa.pub
```









```
kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.11.102:6443     --kubeconfig=/etc/kubernetes/admin.kubeconfig
kubectl config set-credentials kubernetes-admin     --client-certificate=/etc/kubernetes/pki/admin.pem     --client-key=/etc/kubernetes/pki/admin-key.pem     --embed-certs=true     --kubeconfig=/etc/kubernetes/admin.kubeconfig
```









```
kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.11.102:6443    --kubeconfig=/etc/kubernetes/admin.kubeconfig
```

```
kubectl config set-credentials kubernetes-admin     --client-certificate=/etc/kubernetes/pki/admin.pem     --client-key=/etc/kubernetes/pki/admin-key.pem     --embed-certs=true     --kubeconfig=/etc/kubernetes/admin.kubeconfig
```













```
export ETCDCTL_API=3 
etcdctl --endpoints="https://192.168.11.101:2379,https://192.168.11.130:2379,https://192.168.11.134:2379" --cacert=/etc/etcd/ssl/etcd-ca.pem --cert=/etc/etcd/ssl/etcd.pem --key=/etc/etcd/ssl/etcd-key.pem  endpoint status --write-out=table
```



```
[root@k8s-master01 pki]# cat /usr/lib/systemd/system/kube-apiserver.service 

[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes
After=network.target

[Service]
ExecStart=/usr/local/bin/kube-apiserver \
      --v=2  \
      --logtostderr=true  \
      --allow-privileged=true  \
      --bind-address=0.0.0.0  \
      --secure-port=6443  \
      --insecure-port=0  \
      --advertise-address=192.168.11.102 \
      --service-cluster-ip-range=10.96.0.0/12  \
      --service-node-port-range=30000-32767  \
      --etcd-servers=https://192.168.11.101:2379,https://192.168.11.130:2379,https://192.168.11.134:2379 \
      --etcd-cafile=/etc/etcd/ssl/etcd-ca.pem  \
      --etcd-certfile=/etc/etcd/ssl/etcd.pem  \
      --etcd-keyfile=/etc/etcd/ssl/etcd-key.pem  \
      --client-ca-file=/etc/kubernetes/pki/ca.pem  \
      --tls-cert-file=/etc/kubernetes/pki/apiserver.pem  \
      --tls-private-key-file=/etc/kubernetes/pki/apiserver-key.pem  \
      --kubelet-client-certificate=/etc/kubernetes/pki/apiserver.pem  \
      --kubelet-client-key=/etc/kubernetes/pki/apiserver-key.pem  \
      --service-account-key-file=/etc/kubernetes/pki/sa.pub  \
      --service-account-signing-key-file=/etc/kubernetes/pki/sa.key  \
      --service-account-issuer=https://kubernetes.default.svc.cluster.local \
      --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname  \
      --enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,ResourceQuota  \
      --authorization-mode=Node,RBAC  \
      --enable-bootstrap-token-auth=true  \
      --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.pem  \
      --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.pem  \
      --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client-key.pem  \
      --requestheader-allowed-names=aggregator  \
      --requestheader-group-headers=X-Remote-Group  \
      --requestheader-extra-headers-prefix=X-Remote-Extra-  \
      --requestheader-username-headers=X-Remote-User
      # --token-auth-file=/etc/kubernetes/token.csv

Restart=on-failure
RestartSec=10s
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```

```
kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.11.102:6443     --kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig

```

```
kubectl config set-credentials tls-bootstrap-token-user     --token=c8ad9c.2e4d610cf3e7426e --kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig
```

```

kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.11.102:6443     --kubeconfig=${K8S_DIR}/kube-proxy.kubeconfig


```



```
curl https://192.168.11.102:6443/ --cacert /etc/kubernetes/pki/ca.pem --cert /etc/kubernetes/pki/kubernetes.pem --key /etc/kubernetes/cert/kubernetes-key.pem
```



````
systemctl stop kubelet kube-proxy kube-apiserver kube-controller-manager kube-scheduler kube-proxy 
systemctl enable  kubelet kube-proxy kube-apiserver kube-controller-manager kube-scheduler  kube-proxy

````



```
kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.11.102:6443     --kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig
kubectl config set-credentials tls-bootstrap-token-user     --token=c8ad9c.2e4d610cf3e7426e --kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig
kubectl config set-context tls-bootstrap-token-user@kubernetes     --cluster=kubernetes     --user=tls-bootstrap-token-user     --kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig
kubectl config use-context tls-bootstrap-token-user@kubernetes     --kubeconfig=/etc/kubernetes/bootstrap-kubelet.kubeconfig

```





```

apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
address: 0.0.0.0
port: 10250
readOnlyPort: 10255
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 2m0s
    enabled: true
  x509:
    clientCAFile: /etc/kubernetes/pki/ca.pem
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 5m0s
    cacheUnauthorizedTTL: 30s
cgroupDriver: systemd
cgroupsPerQOS: true
clusterDNS:
- 10.96.0.10
clusterDomain: cluster.local
containerLogMaxFiles: 5
containerLogMaxSize: 10Mi
contentType: application/vnd.kubernetes.protobuf
cpuCFSQuota: true
cpuManagerPolicy: none
cpuManagerReconcilePeriod: 10s
enableControllerAttachDetach: true
enableDebuggingHandlers: true
enforceNodeAllocatable:
- pods
eventBurst: 10
eventRecordQPS: 5
evictionHard:
  imagefs.available: 15%
  memory.available: 100Mi
  nodefs.available: 10%
  nodefs.inodesFree: 5%
evictionPressureTransitionPeriod: 5m0s
failSwapOn: true
fileCheckFrequency: 20s
hairpinMode: promiscuous-bridge
healthzBindAddress: 127.0.0.1
healthzPort: 10248
httpCheckFrequency: 20s
imageGCHighThresholdPercent: 85
imageGCLowThresholdPercent: 80
imageMinimumGCAge: 2m0s
iptablesDropBit: 15
iptablesMasqueradeBit: 14
kubeAPIBurst: 10
kubeAPIQPS: 5
makeIPTablesUtilChains: true
maxOpenFiles: 1000000
maxPods: 110
nodeStatusUpdateFrequency: 10s
oomScoreAdj: -999
podPidsLimit: -1
registryBurst: 10
registryPullQPS: 5
resolvConf: /etc/resolv.conf
rotateCertificates: true
runtimeRequestTimeout: 2m0s
serializeImagePulls: true
staticPodPath: /etc/kubernetes/manifests
streamingConnectionIdleTimeout: 4h0m0s
syncFrequency: 1m0s
volumeStatsAggPeriod: 1m0s


```





```
 for FILE in etcd-ca.pem etcd.pem etcd-key.pem; do
     sudo cp $FILE /etc/etcd/ssl/
  done
```

```
 for FILE in ca-key.pem ca.pem front-proxy-ca.pem; do
     sudo cp $FILE /etc/kubernetes/pki/${FILE}
  done
```

```
kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.11.102:6443     --kubeconfig=${K8S_DIR}/kube-proxy.kubeconfig
```



```
apiVersion: projectcalico.org/v3
kind: CalicoAPIConfig
metadata:
spec:
  datastoreType: "etcd"
  etcdEndpoints: "https://192.168.11.101:2379,https://192.168.11.130:2379,https://192.168.11.134:2379"
  etcdKeyFile: "/etc/kubernetes/pki/etcd/etcd-key.pem"
  etcdCertFile: "/etc/kubernetes/pki/etcd/etcd.pem"
  etcdCACertFile: "/etc/kubernetes/pki/etcd/etcd-ca.pem"
  
```

```
etcdctl --endpoints="https://192.168.11.101:2379,https://192.168.11.130:2379,https://192.168.11.134:2379" --cacert=/etc/kubernetes/pki/etcd/etcd-ca.pem --cert=/etc/kubernetes/pki/etcd/etcd.pem --key=/etc/kubernetes/pki/etcd/etcd-key.pem  endpoint status --write-out=table

 
```





```
MIIDxDCCAqygAwIBAgIUaATQ97b1056gjA6JswCttQL9E1AwDQYJKoZIhvcNAQEL
BQAwZzELMAkGA1UEBhMCQ04xEDAOBgNVBAgTB0JlaWppbmcxEDAOBgNVBAcTB0Jl
aWppbmcxDTALBgNVBAoTBGV0Y2QxFjAUBgNVBAsTDUV0Y2QgU2VjdXJpdHkxDTAL
BgNVBAMTBGV0Y2QwIBcNMjEwMzEwMDQwNDAwWhgPMjEyMTAyMTQwNDA0MDBaMGcx
CzAJBgNVBAYTAkNOMRAwDgYDVQQIEwdCZWlqaW5nMRAwDgYDVQQHEwdCZWlqaW5n
MQ0wCwYDVQQKEwRldGNkMRYwFAYDVQQLEw1FdGNkIFNlY3VyaXR5MQ0wCwYDVQQD
EwRldGNkMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA+QlGU0CfV735
fmBkLV/dnAG+p5epfpUlairjJPF9iiPBdqB17MhVl/c7Cku5+CmKU9kwK4Ekm3qE
PFxkDt3HAfdVjkBksTZAzYbWBCx0DIyj5JiBUIYYz8U3F2xT2VJMIurOQefowVkt
mFmg9bBhCaocSm872gbhFX+/T5s8VGTlvLth7dySrop9bxG1MG4Cjb4slW9o10CI
VsW94w9AzT2sFqdfs1c1Psvw93OIBccHecJAigUfF7XYEHBMt5vGnZC+HtUD1iKu
ST3E9q2lu60mElzE4a7sBdJ6NCvWvZe8BTao1FXhYzb22bKRXdQzE40D3OLY472k
JlNNBv53TQIDAQABo2YwZDAOBgNVHQ8BAf8EBAMCAQYwEgYDVR0TAQH/BAgwBgEB
/wIBAjAdBgNVHQ4EFgQU3Lgr+4jLAwvXl6OYuQgSWpxKp2QwHwYDVR0jBBgwFoAU
3Lgr+4jLAwvXl6OYuQgSWpxKp2QwDQYJKoZIhvcNAQELBQADggEBAC3SPE2TiEIs
s0cfh98+FLzVeqwHLNNGnjAgx2eOH391Mc/D5oGuszoEOX30F8NSmDO2O2Fc5lzN
hMeQSXYgn/lefFUgfuN47Y7DpgVL7ccuXud1ZMM/yYLtxD5qByIfC8r5DMb//ha6
WxOwB8CqcF9YOeBJrRqZ+TbNQ6Kg5TdUHGQwZjHIWmI6eW3tuw/33PNKAgroaul7
tt/H0fyWLiHPjJztHrCTaA9pW3SAv2OuKW+ev0aoAuUmQ7Mbzjy5xDRtDHTQxumb
dPkss3i3lkJHVttdqJAjksIYFCDuX+JHoRDRab9Sgmyrg1jGZIOtEiQCS4G1VSKg
MybNz2KAp9M=

```





```
kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.11.102:6443     --kubeconfig=${K8S_DIR}/kube-proxy.kubeconfig
```





```
kubectl -n kube-system create serviceaccount kube-proxy

kubectl create clusterrolebinding system:kube-proxy         --clusterrole system:node-proxier         --serviceaccount kube-system:kube-proxy

SECRET=$(kubectl -n kube-system get sa/kube-proxy \
    --output=jsonpath='{.secrets[0].name}')
JWT_TOKEN=$(kubectl -n kube-system get secret/$SECRET \
--output=jsonpath='{.data.token}' | base64 -d)
PKI_DIR=/etc/kubernetes/pki
K8S_DIR=/etc/kubernetes
kubectl config set-cluster kubernetes     --certificate-authority=/etc/kubernetes/pki/ca.pem     --embed-certs=true     --server=https://192.168.11.102:6443     --kubeconfig=${K8S_DIR}/kube-proxy.kubeconfig

kubectl config set-credentials kubernetes     --token=${JWT_TOKEN}     --kubeconfig=/etc/kubernetes/kube-proxy.kubeconfig
kubectl config set-context kubernetes     --cluster=kubernetes     --user=kubernetes     --kubeconfig=/etc/kubernetes/kube-proxy.kubeconfig
kubectl config use-context kubernetes     --kubeconfig=/etc/kubernetes/kube-proxy.kubeconfig
```

