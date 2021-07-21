# scan 命令



scan 的使用说明：

```
SCAN cursor [MATCH pattern] [COUNT count]
```

- cursor - 游标。
- pattern - 匹配的模式。
- count - 指定从数据集里返回多少元素，默认值为 10 。



返回的是数组列表。



实例：

```redis
redis 127.0.0.1:6379> scan 0   # 使用 0 作为游标，开始新的迭代


redis 127.0.0.1:6379> scan 0 MATCH *11*  # 使用 0 作为游标，开始新的迭代 是否存在为 11的key


redis 127.0.0.1:6379> scan 176 MATCH *11* COUNT 1000 # 使用 176 作为游标，开始新的迭代 是否存在为 11的key
```







## 刪除 Redis 中符合 Pattern 的 Key



用 redis-cli 組合一個清除的指令

```redis
redis-cli --scan --pattern users:* | xargs redis-cli unlink
```

1. 用 scan 找出要刪的 key
2. 透過 xargs 傳給 unlink 刪掉 key



参考文档：

- https://rdbtools.com/blog/redis-delete-keys-matching-pattern-using-scan/
- https://redis.io/commands/scan
- https://redis.io/commands/unlink



