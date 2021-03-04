# redis 相关命令
启动命令

```bash
redis-server /etc/redis.conf
```

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
```

