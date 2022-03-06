# 错误收集

1. 错误信息:Redis command reconnect error=MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on disk. Commands that may modify the data set are disabled. Please check Redis logs for details about the error

    强制关闭Redis快照导致不能持久化。 Redis 运行过程中RDB快照无法写入磁盘中，解决方式有两种
    
    一种是通过redis命令行修改，这种方式方便，直接，更改后直接生效，解决问题。
    命令行修改方式示例：
    
    ```redis
    127.0.0.1:6379> config set stop-writes-on-bgsave-error no 
    ```
    
    另一种是直接修改redis.conf配置文件，但是更改后需要重启redis
    
    修改redis.conf文件：
    vim 打开redis-server配置的redis.conf文件，然后使用快捷匹配模式：/stop-writes-on-bgsave-error 定位到 stop-writes-on-bgsave-error字符串所在位置，接着把后面的yes设置为no。

