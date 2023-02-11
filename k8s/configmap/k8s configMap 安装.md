# ConfigMap挂载的Env

explain:

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 1
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: harbor-001.jimmysong.io/library/nginx:1.9
        ports:
        - containerPort: 80
        envFrom:
        - configMapRef:
            name: env-config
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: env-config
  namespace: default
data:
  log_level: INFO
```

获取环境变量的值

```bash
kubectl exec `kubectl get pods -l run=my-nginx  -o=name|cut -d "/" -f2` env|grep log_level
log_level=INFO
```

修改 ConfigMap

```bash
kubectl edit configmap env-config
```

修改 log_level 的值为 DEBUG。

再次查看环境变量的值。

```bash
kubectl exec `kubectl get pods -l run=my-nginx  -o=name|cut -d "/" -f2` env|grep log_level
log_level=INFO
```

实践证明修改 ConfigMap 无法更新容器中已注入的环境变量信息。

更新使用ConfigMap挂载的Volume

使用下面的配置创建 nginx 容器测试更新 ConfigMap 后容器内挂载的文件是否也跟着更新。

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 1
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: harbor-001.jimmysong.io/library/nginx:1.9
        ports:
        - containerPort: 80
        volumeMounts:
        - name: config-volume
          mountPath: /etc/config
      volumes:
        - name: config-volume
          configMap:
            name: special-config
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: special-config
  namespace: default
data:
  log_level: INFO
```

查看环境变量的值。

```bash
kubectl exec `kubectl get pods -l run=my-nginx  -o=name|cut -d "/" -f2` cat /etc/config/log_level
INFO
```

修改 ConfigMap
```bash
kubectl edit configmap special-config
```

修改 log_level 的值为 DEBUG。

等待大概10秒钟时间，再次查看环境变量的值。

```bash
kubectl exec `kubectl get pods -l run=my-nginx  -o=name|cut -d "/" -f2` cat /etc/config/log_level
DEBUG
```

**总结**

更新 ConfigMap 后：

1. 使用该 ConfigMap 挂载的 Env 不会同步更新
    
2. 使用该 ConfigMap 挂载的 Volume 中的数据需要一段时间（实测大概10秒）才能同步更新

ENV 是在容器启动的时候注入的，启动之后 kubernetes 就不会再改变环境变量的值，且同一个 namespace 中的 pod 的环境变量是不断累加的，参考 Kubernetes中的服务发现与docker容器间的环境变量传递源码探究。为了更新容器中使用 ConfigMap 挂载的配置，需要通过滚动更新 pod 的方式来强制重新挂载 ConfigMap。

