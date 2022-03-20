# ingress-nginx

参考连接：
https://kubernetes.github.io/ingress-nginx/

## ingress

### Deployment
**annotation**

Rewriting can be controlled using the following annotations:

| Name                                           | Description                                                                                                         | Values |
|------------------------------------------------|---------------------------------------------------------------------------------------------------------------------|--------|
| nginx.ingress.kubernetes.io/rewrite-target     | Target URI where the traffic must be redirected                                                                     | string |
| nginx.ingress.kubernetes.io/ssl-redirect       | Indicates if the location section is only accessible via SSL (defaults to True when Ingress contains a Certificate) | bool   |
| nginx.ingress.kubernetes.io/force-ssl-redirect | Forces the redirection to HTTPS even if the Ingress is not TLS Enabled                                              | bool   |
| nginx.ingress.kubernetes.io/app-root           | Defines the Application Root that the Controller must redirect if it's in / context                                 | string |
| nginx.ingress.kubernetes.io/use-regex          | 	Indicates if the paths defined on an Ingress use regular expressions                                               | bool   |



### Examples

重写指定目标(rewrite-target),创建一个 ingress 重写规则的annotation：

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$2
  name: rewrite  // ingress 名字
  namespace: default  // 指定空间
spec:
  ingressClassName: nginx
  rules:
  - host: rewrite.bar.com  // 域名
    http:
      paths:
      - path: /something(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: http-svc
            port: 
              number: 80  // 服务端口号
```
这个例子,这个 ingress 定义将遵循结果以下重写
```sh
rewrite.bar.com/something rewrites to rewrite.bar.com/
rewrite.bar.com/something/ rewrites to rewrite.bar.com/
rewrite.bar.com/something/new rewrites to rewrite.bar.com/new
```

Create an Ingress rule with an app-root annotation:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/app-root: /app1
  name: approot
  namespace: default
spec:
  ingressClassName: nginx
  rules:
  - host: approot.bar.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: http-svc
            port: 
              number: 80
```
参考文档：https://github.com/kubernetes/ingress-nginx/blob/main/docs/examples/rewrite/README.md
