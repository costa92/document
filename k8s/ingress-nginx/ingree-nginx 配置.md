# ingree-nginx 配置

## 新建ingress

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
  name: nginx-example
  namespace: ns-test
spec:
  rules:
    - host: tt.nginxtest.com
      http:
        paths:
          - backend:
              serviceName: nx-service
              servicePort: 80
            path: /          
```

**注意**: extensions/v1beta1 已经废弃,但是可以使用在apply 会有提示
```yamml
[root@k8s-master nginx]# kubectl apply -f ingree-nginx.yaml

Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/nginx-example created
```
因此不要使用：extensions/v1beta1 改使用: networking.k8s.io/v1beta1 或 networking.k8s.io/v1

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
  name: nginx-example
  namespace: ns-test
spec:
# rules 一个 Ingress 可以配置多个 rules
  rules:  
  # host 域名配置，可以不写，匹配*. 可以写成 *.bar.com
    - host: tt.nginxtest.com  
      http:
       # paths 相对于 nginx 的 location 配合。 同一个host 可以配置多个path:   /  /aba 
        paths:
          - backend:
              serviceName: nx-service
              servicePort: 80
            path: /          
```

networking.k8s.io/v1beta1 与 networking.k8s.io/v1 的配置不一样稍微注意



## 多域名配置


```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
  name: nginx-example
  namespace: ns-test
spec:
# rules 一个 Ingress 可以配置多个 rules
  rules:  
  # host 域名配置，可以不写，匹配*. 可以写成 *.bar.com
    - host: tt.nginxtest.com  
      http:
       # paths 相对于 nginx 的 location 配合。 同一个host 可以配置多个path:   /  /aba 
        paths:
          - backend:
              serviceName: nx-service
              servicePort: 80
            path: /     
    - host: tt2.nginxtest.com  
      http:
       # paths 相对于 nginx 的 location 配合。 同一个host 可以配置多个path:   /  /aba 
        paths:
          - backend:
              serviceName: nx-service
              servicePort: 80
            path: /        
```

## 更新命令


```sh
kubectl replace -f ingree-nginx.yaml
```


