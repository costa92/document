# 创建 nginx 服务

创建 nginx-namespace.yaml

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: ns-test
  labels:
    name: label-test
```

创建 nginx-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: ns-test
  name: nginx-deployment
spec:
  selector: 
    matchLabels:
      app: nginx
  replicas: 1
  template: 
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80        
```

创建 nginx-service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: ns-test
spec:
  selector:
    app: nginx
  type: NodePort
  prots:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 3001
```

使用 kubectl 创建服务

```shell
// 创建 namespace
kubectl create -f nginx-namespace.yaml
// 查询 空间消息
kubectl get namespace

// 创建 deployment
kubectl create -f nginx-deployment.yaml

// 创建 service
kubectl create -f nginx-service.yaml

// 查询 deployment 部署状态
kubectl get pods -o wide -n ns-test

```

