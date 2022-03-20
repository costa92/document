# redis 主从安装

### 准备安装 

**环境：** 

| 主机名字  | ip             |
|-------|:---------------|¬
| maste | 192.168.11.143 |
| slave1 | 192.168.11.134 |
| slave2 | 10.10.12.225 |


**下载 redis：**
```bash
 wget http://download.redis.io/releases/redis-5.0.9.tar.gz
 
 # 官网下载慢，可以去其他仓库下载下面是华为的
 wget https://mirrors.huaweicloud.com/redis/redis-5.0.9.tar.gz
```
**解压：**
```bash
tar -zxf redis-5.0.9.tar.gz && cd redis-5.0.9/
```
    
**编译安装：**

```bash   
    # 编译安装需要 gcc
    安装 gcc
    apt-get install gcc -y
    # 开始编译
    cd redis-5.0.9/
    make && make install
```
    再进入utils目录，执行脚本./install_server.sh

**开始配置**

```bash
### redis  配置文件修改:
## redis-1 Master 配置文件修改：
#bind 192.168.11.143         # 关闭了保护模式，这行就可以不需要。
daemonize yes               # 默认为no 一定要打开
protected-mode no           # 默认为yes，这里一定要改成no。关闭保护模式
port 6379
logfile "/data/logs/redis.logs"
databases 16
requirepass 123456    # 从服务密码设置(访问本机数据连接的Auth密码)
masterauth 123456     # 若主服务设置了密码需要加上,在设置哨兵时主从之间连接需要(变更主从需要连接master 的密码.建议主从2个选项都设置上)
maxclients 10000
appendonly yes                         # 本地最好打开AOF模式,不会因为重启丢失数据。
appendfilename "appendonly.aof"

## redis-1 slave 配置文件修改：
# config:
daemonize yes 
protected-mode no
port 6379
logfile "/data/logs/redis.logs"
#slaveof  192.168.11.134 6379              #Redis主节点IP  端口 redis 5之前的配置
replicaof 192.168.11.143 6379             #Redis主节点IP  端口  redis 5之后的配置
requirepass 123456                # 客户端连接redis需要用到的密码
masterauth 123456                 #  从库连接主库用到的密码，类似mysql的主从同步账号密码。
appendonly yes                         # 本地最好打开AOF模式,不会因为重启丢失数据。
appendfilename "appendonly.aof"


## redis-2 slave 配置文件修改：
# config:
daemonize yes 
protected-mode no
port 6379
logfile "/data/logs/redis.logs"
#slaveof  192.168.11.134 6379              #Redis主节点IP  端口 redis 5之前的配置
replicaof 192.168.11.143 6379             #Redis主节点IP  端口  redis 5之后的配置requirepass 123456                # 客户端连接redis需要用到的密码
masterauth 123456                 #  从库连接主库用到的密码，类似mysql的主从同步账号密码。
appendonly yes                         # 本地最好打开AOF模式,不会因为重启丢失数据。
appendfilename "appendonly.aof"


***  一个主从有以下信息既可以成功建立：
replicaof 192.168.11.143 6379              #Redis主节点IP  端口
masterauth 123456  
```

**验证主从**
```bash
[root*master]# redis-cli -a 123456
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379> info Replication
# Replication
role:master
connected_slaves:2
slave0:ip=192.168.11.134,port=6379,state=online,offset=1932,lag=0
slave1:ip=10.10.12.225,port=6379,state=online,offset=1932,lag=0
master_replid:57609f7b3e89bb5351ade82b965a9a9df0453bba
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:1932
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:1932
127.0.0.1:6379> 
```

## 哨兵配置与维护

```bash
### sentinel 配置文件修改：
# sentinel-1 
port 26379
daemonize yes                                         # 
pidfile "/var/run/redis-sentinel.pid"
logfile "/data/logs/sentinel.logs"
dir "/tmp"
sentinel myid 300fcc98aae7579f4f5f687454cfcc4787446e74
sentinel deny-scripts-reconfig yes
sentinel monitor mymaster 192.168.11.143 6379 1        # monitor 监控master IP地址和端口，最后的数字1 是有几个哨兵确认即确认主下线。
sentinel auth-pass mymaster 123456                    # 重点改这个选项，连接主的密码。
sentinel config-epoch mymaster 24
sentinel leader-epoch mymaster 24
protected-mode no 
sentinel down-after-milliseconds mymaster 5000         #修改心跳为5000毫秒
sentinel current-epoch 24
```

**安全性**
对于数据比较重要的节点，主节点会通过设置requirepass参数进行密码 验证，这时所有的客户端访问必须使用auth命令实行校验。从节点与主节点 的复制连接是通过一个特殊标识的客户端来完成，因此需要配置从节点的masterauth参数与主节点密码保持一致，这样从节点才可以正确地连接到主 节点并发起复制流程。

**验证sentinel 结果：**

```bash
127.0.0.1:26379> info sentinel
# Sentinel
sentinel_masters:1
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
sentinel_simulate_failure_flags:0
master0:name=mymaster,status=ok,address=192.168.11.143:6379,slaves=2,sentinels=3
127.0.0.1:26379> 
```

**查看monitor 信息，包含publish ping info 等信息：**

```bash
redis-cli -p 6379 -a 123456 
127.0.0.1:6379> monitor
Ok
```