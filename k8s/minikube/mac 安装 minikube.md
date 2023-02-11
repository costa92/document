# mac 安装 minikube

 1. 下载 minikube 

 ```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube
 ```
 注意版本：
 mac 是 m1 的请选择下面地址下载
 ```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-arm64
 ```

 2. minikube 启动

 ```bash
 minikube start
 ```
 注意：因为在国内会有墙，镜像是无法下载，所有需要更改镜像源的地址：
 ```sh
 minikube start --image-mirror-country cn \
    --iso-url=https://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/iso/minikube-v1.6.0.iso \
    --registry-mirror=https://xxxx.mirror.aliyuncs.com
 ```

**--image-mirror-country cn** 修改国家为中国
**--iso-url** 修改镜像的为阿里云
**--registry-mirror** docker镜像为阿里云的镜像地址

**注意:**  如果没有安装docker的话，可以是使用 hyperkit

安装如下

```sh
brew install hyperkit
brew install docker-machine-driver-hyperkit
minikube start --driver=hyperkit
```

启动命令

 ```sh
 minikube start --image-mirror-country cn \
    --driver=hyperkit \
    --iso-url=https://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/iso/minikube-v1.6.0.iso \
    --registry-mirror=https://xxxx.mirror.aliyuncs.com
 ```

指定安装版本  

```sh
 minikube start --image-mirror-country cn  \
  --memory=3072 \
  --driver=hyperkit  --force \
  --kubernetes-version=v1.22.5 \
  --alsologtostderr \
  --registry-mirror=https://vrasnuyn.mirror.aliyuncs.com 
```




  3. 查看是否安装成功

 ```sh
 kubectl get po -A
 ```

 4. 安装 dashboard
```sh
minikube dashboard
```
5. 安装 ingress

```sh
minikube addons enable ingress
```


## 测试案例

启动 minikube

```sh
minikube start
```

查看 dashboard UI

```sh
minikube dashboard
```

设置内存大小 默认是MB  

```sh
minikube config set memory 4096 // 设置4g
minikube config get memory   // 查看
```

设置CPU 数量

```sh
minikube config set cpus 4 // 设置4个
minikube config get cpus   // 查看
```



创建 deployment

```sh
kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
```
**注意** k8s.gcr.io 国内无法访问 可以改成 registry.cn-hangzhou.aliyuncs.com/google_containers

```sh
kubectl create deployment hello-minikube --image=registry.cn-hangzhou.aliyuncs.com/google_containers/echoserver:1.4
```

查看状态

```sh
kubectl get deploy
kubectl get pod
kubectl describe pod
```

创建 service
```sh
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

查看 service 状态

```sh
kubectl get service
```

自动打开浏览器访问 service

```sh
minikube service hello-minikube
```

清除 service

```sh
kubectl delete service hello-minikube
```

清除 deployment
```sh
kubectl delete deployment hello-minikube
```

停止 minikube 虚拟机
```sh
minikube stop
```

删除虚拟机 VM

```sh
minikube delete
```

查看minikube ip
```sh
minikube ip
```

参考：https://minikube.sigs.k8s.io/docs/

