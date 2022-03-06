# 安装Argo CD

1. 安装命令：
```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
这将创建一个新的命名空间argocd，Argo CD服务和应用程序资源将驻留在该命名空间中

2. Argo CD API server

默认情况下，Argo CD API服务器未使用外部IP公开。要访问API服务器，请选择以下技术之一以公开Argo CD API服务器：

端口转发
```sh
[root@master ~]# kubectl port-forward svc/argocd-server -n argocd --address 0.0.0.0 8080:443
```
3. 查询登录 Argo CD 的密码 
```sh
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

参考连接：

1. [argo-cd](https://argo-cd.readthedocs.io/en/stable/getting_started)
2. [argo-cd 中文介绍](https://blog.csdn.net/weixin_38320674/article/details/118663583)