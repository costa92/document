# 生成目录的 yaml

### go-server api的yaml 文件代码：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: ns-ml
  name: grid-api-test-deployment
spec:
  selector:
    matchLabels:
      app: grid-test-api
  replicas: 1
  template:
    metadata:
      labels:
        app: grid-test-api
    spec:
      containers:
        - name: grid-test-api
          image: 192.168.11.130/ml/grid-service-api:test
          ports:
            - containerPort: 18310
          command: ["./grid.sh", "-f", "etc/grid-api-test-k8s.yaml"]
          lifecycle:
            preStop:
              exec:
                command: ["/bin/sh","/app/etc/cance.sh"]
```
注意：
    command: 执行sh 文件的时候，不需要 加 -c 来处理：
    错误写法
    
    ```yaml
    command: ["/bin/sh","-c","/app/etc/cance.sh"]
    ```
    
    正确处理：
    
    ```yaml
     command: ["/bin/sh","/app/etc/cance.sh"]
    ```    
    