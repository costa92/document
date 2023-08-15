# traefik 的介绍



### 1. 官方文档

https://doc.traefik.io/traefik/

### 2. 简介

  traefik是一个使你把微服务暴露出来变的更容易的http反向代理和负载均衡软件。traefik支持K8S、docker swarm、mesos、consul、etcd、zookeeper等基础设施组件，动态的应用它的配置文件设置。

### 3. 流量示意图

![image.png](http://img.longqiuhong.com/picgo/img/1609419980184-6760533f-b1f2-45c8-85bd-823cce827425.png#height=751&id=Dzkqr&margin=%5Bobject%20Object%5D&name=image.png)



 代理入口

![image.png](http://img.longqiuhong.com/picgo/img/1609420067721-d397157f-f360-47a9-9c62-b0443591e11c.png#height=1050&id=MJpMs&margin=%5Bobject%20Object%5D&name=image.png)

路由：

![routers](http://img.longqiuhong.com/picgo/img/routers.png)

### 4. 核心概念

当启动Traefik时，需要定义`entrypoints`，然后通过entrypoints的路由来分析传入的请求，来查看他们是否是一组规则匹配，如果匹配，则路由可能将请求通过一系列的转换过来在发送到服务上去。

![image.png](http://img.longqiuhong.com/picgo/img/1609466186456-669f7fe8-de5e-481d-b4f4-b3801c15e2bc.png#height=1493&id=cUQRA&margin=%5Bobject%20Object%5D&name=image.png)

- `Providers` 是基础组件，traefik的配置发现是通过它来实现，它可以是协调器，容器引擎，云提供商或键值存储，通过查询 Providers 的API来查询路由的相关信息，一旦检查变化，就会动态更新路由
- `Entrypoints`监听传入的流量，是网络的入口点，定义了接受请求的端口(HTTP或者TCP)
- `Routers`分析请求(host,path,headers,SSL等)，负责将传入的请求连接到可以处理这些请求的服务上去
- `Service`将请求转发给应用，负责配置如何最终将处理传入请求的实际服务
- `Middlewares`中间件，用来修改请求或者根据请求来做出判断，中间件被附件到路由上，是一种在请求发送到服务之前调整请求的一种方法

### 5. 路由规则

路由类型分为三种，分别为：`http`、`tcp`、`udp`

路由规则是指，Traefik接收到的请求，根据给定规则路由到不同的服务中。

| Rule                                                         | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Headers(`key`, `value`)                                      | Check if there is a key keydefined in the headers, with the value value |
| HeadersRegexp(`key`, `regexp`)                               | Check if there is a key keydefined in the headers, with a value that matches the regular expression regexp |
| Host(`example.com`, …)                                       | Check if the request domain (host header value) targets one of the given domains. |
| HostHeader(`example.com`, …)                                 | Check if the request domain (host header value) targets one of the given domains. |
| HostRegexp(`example.com`, `{subdomain:[a-z]+}.example.com`, …) | Check if the request domain matches the given regexp.        |
| Method(`GET`, …)                                             | Check if the request method is one of the given methods (GET, POST, PUT, DELETE, PATCH, HEAD) |
| Path(`/path`, `/articles/{cat:[a-z]+}/{id:[0-9]+}`, …)       | Match exact request path. It accepts a sequence of literal and regular expression paths. |
| PathPrefix(`/products/`, `/articles/{cat:[a-z]+}/{id:[0-9]+}`) | Match request prefix path. It accepts a sequence of literal and regular expression prefix paths. |
| Query(`foo=bar`, `bar=baz`)                                  | Match Query String parameters. It accepts a sequence of key=value pairs. |
| ClientIP(`10.0.0.0/16`, `::1`)                               | Match if the request client IP is one of the given IP/CIDR. It accepts IPv4, IPv6 and CIDR formats |

这个正则配起来稍微有点小坑

为了对`Host`和`Path`使用正则表达式，需要声明一个任意命名的变量，然后跟上用冒号分隔的正则表达式，所有这些都用花括号括起来。

```yaml
HostRegexp(`grafana.{domain:.*}`)
```

### 6. 服务

![services](http://img.longqiuhong.com/picgo/img/services.png)

服务负责配置如何到达实际的服务，最终将处理传入的请求。使用`service`定义：

```yaml
http:
  services:
    traefik:
      loadBalancer:
        servers:
          - url: "http://127.0.0.1:10000"
```

​	更多配置，可以参考：[官网介绍](https://doc.traefik.io/traefik/routing/services/)

### 7. 中间件

![middleware](http://img.longqiuhong.com/picgo/img/middleware.png)

从图中基本可以明白中间件的作用，也可以理解成拦截器。
Traefik中有几种可用的中间件：一些可以修改请求、请求头，一些负责重定向，一些可以添加身份验证等等。

下面是一个官网给出的示例：

```yaml
# As YAML Configuration File
http:
  routers:
    router1:
      service: myService
      middlewares:
        - "foo-add-prefix"
      rule: "Host(`example.com`)"

  middlewares:
    foo-add-prefix:
      addPrefix:
        prefix: "/foo"

  services:
    service1:
      loadBalancer:
        servers:
          - url: "http://127.0.0.1:80"
```



### 参考连接

- [Traefik源码仓库](https://github.com/traefik/traefik)
- [Traefik官网](https://traefik.io/)
- [Traefik静态配置项-File provider](https://doc.traefik.io/traefik/reference/static-configuration/file/)
- [Let’s Encrypt](https://letsencrypt.org/)
- [Systemd文档](https://www.freedesktop.org/software/systemd/man/systemd.unit.html)
- [ormissia blog](https://ormissia.github.io/posts/deployment/3003-linux-traefik/)