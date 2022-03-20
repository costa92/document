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
```