# Grafana

# 添加 repo 并更新
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# 注意这里后面是 -stack 的是默认打包好在一起的，没stack的是默认只有 loki 的，自定义安装请参考官网给出的命令
helm upgrade --install loki --namespace=<YOUR-NAMESPACE> grafana/loki-stack
helm upgrade --install loki --namespace=ns-ml grafana/loki-stack

# 安装 grafana
helm install loki-grafana --namespace=<YOUR-NAMESPACE> grafana/grafana

helm install loki-grafana --namespace=ns-ml grafana/grafana
# 查看 grafana 的密码
kubectl get secret --namespace <YOUR-NAMESPACE> loki-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

kubectl get secret --namespace ns-ml loki-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
   
# 临时暴露 端口 进行访问测试
kubectl port-forward --namespace <YOUR-NAMESPACE> service/loki-grafana 3000:80

kubectl port-forward --namespace ns-ml service/loki-grafana 3000:80

// 外网访问：
kubectl port-forward --namespace ns-ml --address 0.0.0.0 service/loki-grafana 3000:80