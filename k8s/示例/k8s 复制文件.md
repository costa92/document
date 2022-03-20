# k8s拷贝容器里面文件到宿主机本地文件命令方法

kubectl cp 命名空间/容器名:/容器目录/文件名 /宿主机目录/文件名

```
 kubectl cp ns-ml/grid-api-deployment-556b9db88c-9kkhs:/app/grid.sh  /home/api.sh
 ```
 
 

scp root@192.168.11.102:/home/api.sh api.sh 


longqh456258
 
