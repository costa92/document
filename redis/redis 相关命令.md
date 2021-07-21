# redis 相关命令
启动命令

```bash
redis-server /etc/redis.conf
# mac
redis-server /opt/homebrew/etc/redis.conf
```

<<<<<<< HEAD
查看redis的配置文件地址

```sh
redis-cli   # 进入redis 
127.0.0.1:6379> info server
```

输出：
```redis
# Server
redis_version:6.0.4
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:8e6667c19bc7b263
redis_mode:standalone
os:Darwin 19.6.0 x86_64
arch_bits:64
multiplexing_api:kqueue
atomicvar_api:atomic-builtin
gcc_version:4.2.1
process_id:41883
run_id:817bbb8ada070ce9177d92128d6671224c7c8fa0
tcp_port:6379
uptime_in_seconds:4550796
uptime_in_days:52
hz:10
configured_hz:10
lru_clock:3538392
executable:/Users/costalong/redis-server
config_file:/usr/local/etc/redis.conf
=======
查看redis信息

```
redis-cli
```

```
127.0.0.1:6379> info Server

redis_version:6.2.1
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:520866636212549
redis_mode:standalone
os:Darwin 20.3.0 arm64
arch_bits:64
multiplexing_api:kqueue
atomicvar_api:c11-builtin
gcc_version:4.2.1
process_id:484
process_supervised:no
run_id:1a346a4e877c9bc308a7d7d39bd8020c93a53a66
tcp_port:6379
server_time_usec:1618326130790042
uptime_in_seconds:3485
uptime_in_days:0
hz:10
configured_hz:10
lru_clock:7713394
executable:/opt/homebrew/opt/redis/bin/redis-server
config_file:/opt/homebrew/etc/redis.conf
io_threads_active:0
```

查看配置信息

```
config get *
```

修改配置信息：

```
# 后台运行
daemonize no/yes   # 否/是

>>>>>>> 9eb4aa2019a5cc3a5aa8496ea15154e1dff31ee5
```

