

# etcd 集群安装

## 下载安装 etcd

在 github 下载 [releases](https://github.com/etcd-io/etcd/releases) 选择版本下载

```bash
wget https://github.com/etcd-io/etcd/releases/download/v3.4.13/etcd-v3.4.13-linux-amd64.tar.gz
```

解压安装：

```bash
tar -zxvf etcd-v3.4.13-linux-amd64.tar.gz --strip-components=1 -C /usr/local/bin etcd-v3.4.13-linux-amd64/etcd{,ctl}
```

查看是否成功

```
etcdctl version
```



## 生成证书

下载生成证书工具

```bash
wget "https://pkg.cfssl.org/R1.2/cfssl_linux-amd64" -O /usr/local/bin/cfssl
wget "https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64" -O /usr/local/bin/cfssljson
chmod +x /usr/local/bin/cfssl /usr/local/bin/cfssljson
```

创建证书

创建 ca-config.json 文件

```
{
  "signing": {
    "default": {
      "expiry": "876000h"
    },
    "profiles": {
      "kubernetes": {
        "usages": [
            "signing",
            "key encipherment",
            "server auth",
            "client auth"
        ],
        "expiry": "876000h"
      }
    }
  }
}
```

创建 etcd-ca-csr.json

```
{
  "CN": "etcd",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "Beijing",
      "L": "Beijing",
      "O": "etcd",
      "OU": "Etcd Security"
    }
  ],
  "ca": {
    "expiry": "876000h"
  }
}
```

创建 etcd-csr.json

```
{
  "CN": "etcd",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "Beijing",
      "L": "Beijing",
      "O": "etcd",
      "OU": "Etcd Security"
    }
  ]
}
```

生成证书的CSR文件：证书签名请求文件，配置了一些域名、公司、单位

```
# 生成etcd CA证书和CA证书的key
cfssl gencert -initca etcd-ca-csr.json | cfssljson -bare /etc/etcd/ssl/etcd-ca
```

```
cfssl gencert \
   -ca=/etc/etcd/ssl/etcd-ca.pem \
   -ca-key=/etc/etcd/ssl/etcd-ca-key.pem \
   -config=ca-config.json \
   -hostname=127.0.0.1,192.168.11.101,192.168.11.130,192.168.11.134 \
   -profile=kubernetes \
   etcd-csr.json | cfssljson -bare /etc/etcd/ssl/etcd
```

将证书复制到其他节点

```bash
Nodes='192.168.11.134,192.168.11.130'
for NODE in $Nodes; do
     ssh $NODE "mkdir -p /etc/etcd/ssl"
     for FILE in etcd-ca-key.pem  etcd-ca.pem  etcd-key.pem  etcd.pem; do
       scp /etc/etcd/ssl/${FILE} $NODE:/etc/etcd/ssl/${FILE}
     done
 done
```



vim /etc/etcd/etcd.config.yml

```
name: 'etcd01'
data-dir: /var/lib/etcd
wal-dir: /var/lib/etcd/wal
snapshot-count: 5000
heartbeat-interval: 100
election-timeout: 1000
quota-backend-bytes: 0
listen-peer-urls: 'https://192.168.11.101:2380'
listen-client-urls: 'https://192.168.11.101:2379,http://127.0.0.1:2379'
max-snapshots: 3
max-wals: 5
cors:
initial-advertise-peer-urls: 'https://192.168.11.101:2380'
advertise-client-urls: 'https://192.168.11.101:2379'
discovery:
discovery-fallback: 'proxy'
discovery-proxy:
discovery-srv:
initial-cluster: 'etcd01=https://192.168.11.101:2380,etcd02=https://192.168.11.130:2380,etcd03=https://192.168.11.134:2380'
initial-cluster-token: 'etcd-cluster'
initial-cluster-state: 'new'
strict-reconfig-check: false
enable-v2: true
enable-pprof: true
proxy: 'off'
proxy-failure-wait: 5000
proxy-refresh-interval: 30000
proxy-dial-timeout: 1000
proxy-write-timeout: 5000
proxy-read-timeout: 0
client-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
peer-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  peer-client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
debug: false
log-package-levels:
log-outputs: [default]
force-new-cluster: false
```



其他节点：

```bash
name: 'etcd02'
data-dir: /var/lib/etcd
wal-dir: /var/lib/etcd/wal
snapshot-count: 5000
heartbeat-interval: 100
election-timeout: 1000
quota-backend-bytes: 0
listen-peer-urls: 'https://192.168.11.130:2380'
listen-client-urls: 'https://192.168.11.130:2379,http://127.0.0.1:2379'
max-snapshots: 3
max-wals: 5
cors:
initial-advertise-peer-urls: 'https://192.168.11.130:2380'
advertise-client-urls: 'https://192.168.11.130:2379'
discovery:
discovery-fallback: 'proxy'
discovery-proxy:
discovery-srv:
initial-cluster: 'etcd01=https://192.168.11.101:2380,etcd02=https://192.168.11.130:2380,etcd03=https://192.168.11.134:2380'
initial-cluster-token: 'etcd-cluster'
initial-cluster-state: 'new'
strict-reconfig-check: false
enable-v2: true
enable-pprof: true
proxy: 'off'
proxy-failure-wait: 5000
proxy-refresh-interval: 30000
proxy-dial-timeout: 1000
proxy-write-timeout: 5000
proxy-read-timeout: 0
client-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
peer-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  peer-client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
debug: false
log-package-levels:
log-outputs: [default]
force-new-cluster: false
```



```
name: 'etcd03'
data-dir: /var/lib/etcd
wal-dir: /var/lib/etcd/wal
snapshot-count: 5000
heartbeat-interval: 100
election-timeout: 1000
quota-backend-bytes: 0
listen-peer-urls: 'https://192.168.11.134:2380'
listen-client-urls: 'https://192.168.11.134:2379,http://127.0.0.1:2379'
max-snapshots: 3
max-wals: 5
cors:
initial-advertise-peer-urls: 'https://192.168.11.134:2380'
advertise-client-urls: 'https://192.168.11.134:2379'
discovery:
discovery-fallback: 'proxy'
discovery-proxy:
discovery-srv:
initial-cluster: 'etcd01=https://192.168.11.101:2380,etcd02=https://192.168.11.130:2380,etcd03=https://192.168.11.134:2380'
initial-cluster-token: 'etcd-cluster'
initial-cluster-state: 'new'
strict-reconfig-check: false
enable-v2: true
enable-pprof: true
proxy: 'off'
proxy-failure-wait: 5000
proxy-refresh-interval: 30000
proxy-dial-timeout: 1000
proxy-write-timeout: 5000
proxy-read-timeout: 0
client-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
peer-transport-security:
  cert-file: '/etc/kubernetes/pki/etcd/etcd.pem'
  key-file: '/etc/kubernetes/pki/etcd/etcd-key.pem'
  peer-client-cert-auth: true
  trusted-ca-file: '/etc/kubernetes/pki/etcd/etcd-ca.pem'
  auto-tls: true
debug: false
log-package-levels:
log-outputs: [default]
force-new-cluster: false
```

创建 Service

vim /usr/lib/systemd/system/etcd.service

```
[Unit]
Description=Etcd Service
Documentation=https://coreos.com/etcd/docs/latest/
After=network.target

[Service]
Type=notify
ExecStart=/usr/local/bin/etcd --config-file=/etc/etcd/etcd.config.yml
Restart=on-failure
RestartSec=10
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
Alias=etcd3.service
```

节点重启

```
systemctl daemon-reload
systemctl enable --now etcd
```



## 检查

```bash
export ETCDCTL_API=3 
etcdctl --endpoints="https://192.168.11.101:2379,https://192.168.11.130:2379,https://192.168.11.134:2379" --cacert=/opt/certs/etcd-ca.pem --cert=/opt/certs/etcd.pem --key=/opt/certs/etcd-key.pem  endpoint status --write-out=table

```

```
+-----------------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
|          ENDPOINT           |        ID        | VERSION | DB SIZE | IS LEADER | IS LEARNER | RAFT TERM | RAFT INDEX | RAFT APPLIED INDEX | ERRORS |
+-----------------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+
| https://192.168.11.101:2379 | 96e44f71f4a76fb9 |  3.4.13 |   16 kB |     false |      false |      7091 |         12 |                 12 |        |
| https://192.168.11.130:2379 | d0a8c90042e3b75e |  3.4.13 |   20 kB |     false |      false |      7091 |         12 |                 12 |        |
| https://192.168.11.134:2379 | ede8e3865e92bf6b |  3.4.13 |   20 kB |      true |      false |      7091 |         12 |                 12 |        |
+-----------------------------+------------------+---------+---------+-----------+------------+-----------+------------+--------------------+--------+

```

```
etcdctl --endpoints="https://192.168.11.101:2379,https://192.168.11.130:2379,https://192.168.11.134:2379" --cacert=/opt/certs/etcd-ca.pem --cert=/opt/certs/etcd.pem --key=/opt/certs/etcd-key.pem  endpoint health --write-out=table
```

```
+-----------------------------+--------+--------------+-------+
|          ENDPOINT           | HEALTH |     TOOK     | ERROR |
+-----------------------------+--------+--------------+-------+
| https://192.168.11.134:2379 |   true |  93.758377ms |       |
| https://192.168.11.101:2379 |   true | 109.383668ms |       |
| https://192.168.11.130:2379 |   true |  104.63033ms |       |
+-----------------------------+--------+--------------+-------+
```



