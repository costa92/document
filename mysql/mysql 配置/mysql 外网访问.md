# mysql(5.7 ) 外网访问

**一、设置MySQL服务允许外网访问**

修改mysql的配置文件，有的是my.ini（windows），有的是my.cnf（linux），

在配置文件中增加

```sh
[mysqld]
port=3306
bind-address=0.0.0.0
```
然后重新启动mysql服务，执行service mysql restart。

**二、设置mysql用户支持外网访问**

需要使用root权限登录mysql，更新mysql.user表，设置指定用户的Host字段为%，默认一般为127.0.0.1或者localhost。

1. 登录数据库

```sh
mysql -u root -p
```

2.查询

```sql
select user,host from user;
select host, user, authentication_string, plugin from user;
```

3. 创建host

如果没有"%"这个host值,就执行下面这两句:

```sql
mysql> update user set host='%' where user='root';
mysql> flush privileges;
```

4.授权用户
* 任意主机以用户root和密码mypwd连接到mysql服务器

```sql
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'mypwd' WITH GRANT OPTION;
mysql> flush privileges;
```
* IP为192.168.133.128的主机以用户myuser和密码mypwd连接到mysql服务器

```sql
mysql> GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.133.128' IDENTIFIED BY 'mypwd' WITH GRANT OPTION; 
mysql> flush privileges;
## 授权不改密码
mysql> grant all privileges on *.* to 'username'@'%' with grant option; 
mysql> flush privileges;
```

5. 创建用户

```sql
mysql> create USER 'username'@'host' IDENTIFIED BY 'password';
## 密码的加密方式
mysql > create user 'username'@'localhost' identified with mysql_native_password BY 'password';
```

其中`username`为自定义的用户名；`host`为登录域名，`host`为`'%'`时表示为 任意IP，为`localhost`时表示本机，或者填写指定的IP地址；`paasword`为密码

```sql
mysql> CREATE USER 'dog'@'localhost' IDENTIFIED BY '123456';
mysql> CREATE USER 'pig'@'192.168.1.101_' IDENDIFIED BY '123456';
mysql> CREATE USER 'pig'@'%' IDENTIFIED BY '123456';
mysql> CREATE USER 'pig'@'%' IDENTIFIED BY '';
mysql> CREATE USER 'pig'@'%';
```

6  撤销授权

```mysql
#收回权限(不包含赋权权限)
mysql> REVOKE ALL PRIVILEGES ON *.* FROM user_name;
mysql> REVOKE ALL PRIVILEGES ON user_name.* FROM user_name;
#收回赋权权限
mysql> REVOKE GRANT OPTION ON *.* FROM user_name;

#操作完后重新刷新权限
mysql> flush privileges;
```

7 删除用户

```mysql
mysql> DROP USER 'username'@'host';
```

8 授权

授权 select,insert,update,delete 权限给某一个表

```mysql
mysql> grant select,insert,update,delete on database.table to 'opt_crm'@'localhost' with grant option;
```

授权   select,insert,update,delete 给 crm 库，注意如果是全库的用户  * 号代替

```mysql
mysql> grant select,insert,update,delete on crm.* to 'opt_crm'@'localhost' with grant option;
```

