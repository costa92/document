

# grafana 安装

参考官网 [grafana](https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/)



```sh
 sudo /bin/systemctl daemon-reload   // 重新加载某个服务的配置文件，如果新安装了一个服务，归属于 systemctl 管理，要是新服务的服务程序配置文件生效，需重新加
 sudo /bin/systemctl enable grafana-server  // 开机启动
```

### You can start grafana-server by executing

```sh
 sudo /bin/systemctl start grafana-server  // 启动
 
 sudo systemctl status grafana-server //查看状态
```



修改配置信息: /etc/grafana/grafana.ini

```sh
[security]
# disable creation of admin user on first start of grafana
;disable_initial_admin_creation = false    

# default admin user, created on startup
;admin_user = admin   // 默认用户

# default admin password, can be changed before first start of grafana,  or in profile settings
;admin_password = admin   // 默认密码

# used for signing
;secret_key = SW2YcwTIb9zpOOhoPsMm
```

登录地址: http://localhost:3000/login 登录成功需要修改密码.  123456