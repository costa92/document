# helm 常用的命令

helm 官网地址：https://helm.sh/zh/docs/helm/helm_plugin_update/

```sh
## 查看已经安装
helm list -n namespace
## 安装
helm install name repo/pagename

## 搜索
helm search repo pagername

## 查看可安装版本
helm search repo ingress-nginx --versions

## 拉取源码
helm pull repo/pagename 

## 拉取源码 指定版本
helm pull repo/pagename --version=版本号
```