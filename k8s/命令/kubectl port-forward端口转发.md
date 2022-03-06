# kubectl port-forward端口转发

将远程pod端口转发到本地端口
```sh
kubectl port-forward monitoring-grafana-695c545f46-rhtwc --namespace=monitoring 3000:3000
```

**注意：**

'monitoring-grafana-695c545f46-rhtwc' 是pod名字,也可以是podId

:前面是本地端口，:后面是pod上的端口
