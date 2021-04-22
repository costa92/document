# centos7下安装和使用Supervisord

supervisor：要安装的软件的名称。
supervisord：装好supervisor软件后，supervisord用于启动supervisor服务。
supervisorctl：用于管理supervisor配置文件中program和supervisor服务本身。

### 安装：
```shell
yum install epel-release  
yum install -y supervisor  

systemctl enable supervisord  # 开机自启动  
systemctl start supervisord   # 启动supervisord服务  
systemctl status supervisord  # 查看supervisord服务状态  
ps -ef|grep supervisord       # 查看是否存在supervisord进程

```

安装的配置为在 /etc/supervisord.conf
supervisord.conf 文件最后一行的
```shell
files = supervisord.d/*.ini; ini 和 conf 后缀都可以的
```

### 启动
```shell
supervisord -c /etc/supervisord.conf
```
### 查看进程是否存在
```shell
ps -aux |  grep supervisord 
# 或
supervisorctl
```

###  在 /etc/supervisord.d/ 名录添加 配置文件 

```shell
;program名称，随便写，但不要重复，是program的唯一标识
[program:celery_touchscan]
;指定运行目录
directory=/root/TouchScanV2/ 
;运行目录下执行命令
command=celery -A scan worker --queue=touchscan --pidfile="./log/pid.txt" --logfile="./log/scan.log" -c 10
;进程名称
process_name=%(program_name)s_%(process_num)02d
;启动设置
numprocs=1         ;进程数，注意：（celery进程数量,不是work数量，相当于执行了10个command命令，而不是在celery中指定-c 为10）
autostart=true      ;当supervisor启动时,程序将会自动启动
autorestart=true    ;自动重启（当work被kill了之后会重新启动）
;运行程序的用户
;user=root
;startsecs=1 ;程序重启时候停留在runing状态的秒数
;startretries=10 ;启动失败时的最多重试次数
;停止信号,默认TERM
;中断:INT (类似于Ctrl+C)(kill -INT pid)，退出后会将写文件或日志(推荐)
;终止:TERM (kill -TERM pid)
;挂起:HUP (kill -HUP pid),注意与Ctrl+Z/kill -stop pid不同
;从容停止:QUIT (kill -QUIT pid)
stopsignal=INT
```

### 重启

```shell
supervisord -c /etc/supervisord.conf relaod
```

### 关闭
```shell
supervisorctl -c /etc/supervisord.conf shutdown
```

### 其他命令
```shell
supervisorctl status
supervisorctl stop celery_touchscan # celery_touchscan 是一个program的名称
supervisorctl start celery_touchscan
supervisorctl restart celery_touchscan
supervisorctl reread
supervisorctl update
```

#### 子进程问题
有时候用Supervisor托管的程序还会有子进程，如果只杀死主进程，子进程就可能变成孤儿进程。通过以下这两项配置来确保所有子进程都能正确停止：

```shell
stopasgroup=true
killasgroup=true
```
