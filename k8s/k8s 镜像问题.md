# k8s 镜像问题

因为国内墙的问题，导致无法访问 k8s.gcr.io ,因此无法获取 k8s 相关的镜像，而导致无法安装 k8s 集群

1. 可以在阿里云或腾讯云购买云服务器，云服务器一定要在国内国外能访问，通过云服务器下载，在上传到私有镜像库或 hub.docker.com 中，再下载下来
2. 通过阿里云源，阿里云源会定时更新镜像到阿里镜像库中，我们只需从阿里云下载即可。阿里云镜像库地址：registry.cn-hangzhou.aliyuncs.com/google_containers

例子：
```sh
## 下载
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.5

## 更换
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.5 k8s.gcr.io/pause:3.5
```


