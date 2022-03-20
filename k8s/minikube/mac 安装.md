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



  