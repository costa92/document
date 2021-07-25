# tls 证书生成

### 私钥

```sh
openssl ecparam -genkey -name secp384r1 -out server.key
```

### 自签公钥

```sh
openssl req -new -x509 -sha256 -key server.key -out server.pem -days 3650
```

#### 填写信息

```
Country Name (2 letter code) []:  // 国家
State or Province Name (full name) []: //身份
Locality Name (eg, city) []: // 城市
Organization Name (eg, company) []: // 公司名称
Organizational Unit Name (eg, section) []: 
Common Name (eg, fully qualified host name) []:go-grpc-example  // 项目名字
Email Address []:
```

在目录下生成两个文件 server.key,server.pem

