
# slowlog 命令

Redis slowlog 是 Redis 用来记录查询执行时间的日志系统。

查询执行时间指的是不包括像客户端响应(talking) 、 发送回复等IO操作，而单单是执行一个命令所消耗的时间。
另外，slow log 保存爱内存里面，读写速度非常快，因此可以放心的的使用，不必担心开启slow log而影响redis的速度

有两个参数用于配置 slow log：
**slowlog-log-slower-than:** 设定执行时间，单位是毫秒，执行时长超过该时间的命令将会被记入log。 -1表示不记录slow log; 0强制记录所有命令

**slowlog-max-len:** slow log 的长度。最小值为0。如果日志队列已超出最大长度，则最早的记录会被从队列中清除

可以通过编辑 redis.conf 文件配置以上两个参数。对运行中的redis，可以通过config get,config set 命令动态改变上述两个参数


edis slowlog 命令基本语法如下：
```redis
SLOWLOG subcommand [argument]
```

读取 slow log

slow log 是记录在内存中的，所以即使记录所有的命令 （将 slowlog-log-slower-then设为0），对性能的影响也很小
slowlog get: 列出所有 slow log
slowlog get N: 列出最近N条 slow log

每个条目由4个字段构成：
*  用于表示该条slow log的唯一id
*  以 unix 时间戳表示的日志记录时间
* 命令执行时间，单位：微秒
* 执行的具体命令
只有当reids重启后，id编号才会被重置。

查看长度
```redis
slowlog len
```

清空slow log
```redis
slowlog reset
```

注意：一旦重置日志删除了，就无法恢复

### 测试

设定请求的响应时间(R),服务时间(S), 排队延时（Q).
**R = S + Q**

打开两个session 都连接 redis，在session-1 执行
```redis
127.0.0.1:6379> debug sleep 10
OK
(10.00s) # 响应时间
```

在session-2 执行写入命令
```redis
127.0.0.1:6379> set testname slowlog
OK
(5.33s) # 响应时间
```

通过 slowlog 查询执行时间
```redis
127.0.0.1:6379> slowlog get 2
1) 1) (integer) 19
   2) (integer) 1614167959
   3) (integer) 14 # 执行时间只有 14微秒
   4) 1) "set"
      2) "testname"
      3) "slowlog"
   5) "127.0.0.1:60769"
   6) ""
2) 1) (integer) 18
   2) (integer) 1614167959
   3) (integer) 10000654 
   4) 1) "debug"
      2) "sleep"
      3) "10"
   5) "127.0.0.1:60764"
   6) ""
```

那么执行 set testname slowlog 执行时间分析

| 响应时间 | 执行时间 | 排队延时 |
| -------- | ----- | -- |
|  5330000µs |  14μs  |  5329986μs  |


参考其他延迟：https://www.cnblogs.com/wy123/p/10202338.html

### Redis Single-threads的问题

Redis Server是单线程的处理(**bgsave或aof重写时会Fork子进程处理**），同一时间只能处理一个命令，并且是同步完成的。
从上节的测试中可见，set命令服务时间只有4微秒，但被debug sleep 6命令阻塞后，响应时间变成5.14秒。
所以RD和DBA在设计keyspace和访问模式时，应尽量避免使用**耗时较大的命令**。
在理想状态下，Redis单实例能处理8~10w的QPS, 如果大量的redis命令大量耗时大于1ms, 其实QPS只能达到1000基于几百。
Redis出现耗时大的命令，导致其他所有请求被阻塞等待，redis处理能力急剧退化，易导致整个服务链雪崩。


