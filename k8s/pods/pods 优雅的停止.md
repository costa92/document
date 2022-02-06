# pods 优雅停止


phase表示一个Pod处于其生命周期的哪个阶段，一共有以下5个可能的取值：

    Pending：Pod已经被k8s系统接受，但Pod中还有容器没有被创建。Pod被调度前和下载容器镜像的时候都处于这个阶段
    Running：Pod已经被调度到Node上，所有的容器都已经被创建，并且至少有一个容器还在运行中（正在启动或重启中的容器也算）
    Succeeded：Pod中的所有容器都成功停止，并且不会再次重启
    Failed：Pod中的所有容器都已经停止，并且至少有一个容器是以失败停止的（以非0状态退出或被系统强制停止）
    Unknown：由于某种原因无法获得Pod的状态，一般是和Pod所在的Host出现通信问题导致


Pod phase的查看方式：

```bash
kubectl get pods eureka-deployment-7d8b698dc8-xv99v -n ns-ml -o yaml |grep 'phase:'
```

输出结果：
```bash
        f:phase: {}
  phase: Running
```

## lifecycle 用法

lifecycle 周期有两个hook钩子 postStart 与 preStop

1、PostStart hook是在容器创建(created)之后立马被调用，并且PostStart跟容器的ENTRYPOINT是异步执行的，无法保证它们之间的顺序

## 2、PreStop hook是容器处于Terminated状态时立马被调用(也就是说要是Job任务的话，执行完之后其状态为completed，所以不会触发PreStop的钩子)，同时PreStop是同步阻塞的，PreStop执行完才会执行删除Pod的操作


## 3、注意：

PostStart会阻塞容器成为Running状态
    
PreStop会阻塞容器的删除，但是过了    terminationGracePeriodSeconds时间后，容器会被强制删除
    
如果PreStop或者PostStart失败的话, 容器会被杀死

## 4、用法：
钩子的回调函数支持三种方式定义动作：

** exec：在容器内执行命令，如果命令的退出状态码是 0 表示执行成功，否则表示失败**

```yaml
  lifecycle：
    postStart:
      exec:
        command:
        - cat
        - /tmp/healthy
```

**httpGet：向指定 URL 发起 GET 请求，如果返回的 HTTP 状态码在 [200, 400) 之间表示请求成功，否则表示失败**

```yaml
lifecycle：
    postStart:
      httpGet:
        path: /login   # URI地址
        port: 80  # 端口号
        host: 192.168.126.100 # 主机地址
        scheme: HTTP   # 支持的协议，http或https

# http://192.168.126.100:80/login
```


**TCPSocket：在容器尝试访问指定的socket**
```yaml
  lifecycle：
    postStart:
      tcpSocket:
        port: 8080
```


Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
        lifecycle:
          postStart:
            exec:
              command: ["/bin/sh","-c","echo 11 >> /usr/share/nginx/html/index.html"]  # 启动容器应用之后执行
          preStop:
            exec:
              command: ["/bin/sh","-c","echo 'Hello from the preStop handler' >> /var/log/nginx/message"] ## 删除pod 完成之前执行
status: {}
```

使用Eureka 是一个基于 REST 的服务，作为服务注册中心，用于定位服务来进行中间层服务器的负载均衡和故障转移。各服务启动时,会向Eureka Server注册自己的信息(IP,端口,服务信息等),Eureka Server会存储这些信息.微服务启动后,会周期性(默认30秒)的向Eureka Server发送心跳以续约自己的”租期”，并可以从eureka中获取其他微服务的地址信息，执行相关的逻辑考虑现在eureka server 修改注册实例的状态，暂停服务( InstanceStatus.OUT_OF_SERVICE )，保留一段时间后，再删除服务。


禁止注册中使用服务：

```sh
curl -X PUT “http://admin:admin@192.168.101.100:8761/eureka/apps/{appName}/{instanceId}/status?value=OUT_OF_SERVICE"
```

**说明**：admin:admin是eureka的登录名和密码，如果没有，直接去掉前面这段；
instanceId是上面打开的链接显示的服务列表中的标签内容,如：myapp:192.168.1.100:8080

在k8s 应用

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: NAME-service-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: NAME-service
  template:
    metadata:
      labels:
        app: NAME-service
    spec:
      containers:
      - name: NAME-service
        lifecycle:
          preStop:
            exec:
              command:
                - "/bin/sh"
                - "-c"
                - " \
                  APPLICATION=NAME-service; \
                  APPLICATION_PORT=8016; \
                  curl -s -X PUT http://eureka01-server.domain.com/eureka/apps/${APPLICATION}/$(hostname):${APPLICATION}:${APPLICATION_PORT}/status?value=OUT_OF_SERVICE; \
                  sleep 30; \
                  "
```


lifecycle，首先定义了服务名和端口的环境变量，把这部分单独作为变量，便于不同的服务进行修改。使用 curl PUT 到eureka 配置状态为 OUT_OF_SERVICE。配置一个sleep时间，作为服务停止缓冲时间。
