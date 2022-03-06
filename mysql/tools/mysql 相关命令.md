### 连接数据

```
mysql  -u 用户名 -p 密码
```



### 显示数据库

```
show databases;
```

### 使用库

```
use dataname;
```

### 显示表

```
show tables;
```

### 导入数据

```
source $path  # path sql文件的位置
```

### 导入数据

```
mysqldup -u 用户 -p 密码  库名 表名  > 导出的位置
mysqldump -uroot -pdg123456 qysj tb_company > /home/dev/tb_company.sql
```

### 查看 mysql 的配置文件路径

```sql
mysql --help|grep my.cnf 
```