# hash 命令

下表列出了 redis hash 基本的相关命令：

| 序号 | 命令 | 描述   |
| ----: | ---- | :------------ |
| 1 | [HDEL key field1 [field2]](https://www.runoob.com/redis/hashes-hdel.html)| 删除一个或多个哈希表字段 |
| 2 | [HEXISTS key field](https://www.runoob.com/redis/hashes-hexists.html)| 查看哈希表 key 中，指定的字段是否存在。 |
| 3 | [HGET key field](https://www.runoob.com/redis/hashes-hget.html)| 获取存储在哈希表中指定字段的值。 |
| 4 | [HGETALL key](https://www.runoob.com/redis/hashes-hgetall.html)| 获取在哈希表中指定 key 的所有字段和值 |
| 5 | [HINCRBY key field increment](https://www.runoob.com/redis/hashes-hincrby.html)| 为哈希表 key 中的指定字段的整数值加上增量 increment 。 |
| 6 | [HINCRBYFLOAT key field increment](https://www.runoob.com/redis/hashes-hincrbyfloat.html)| 为哈希表 key 中的指定字段的浮点数值加上增量 increment 。 |
| 7 | [HKEYS key](https://www.runoob.com/redis/hashes-hkeys.html)| 获取所有哈希表中的字段 |
| 8 | [HLEN key](https://www.runoob.com/redis/hashes-hlen.html)| 获取哈希表中字段的数量 |
| 9 | [HMGET key field1 [field2]](https://www.runoob.com/redis/hashes-hmget.html)| 获取所有给定字段的值 |
| 10 | [HMSET key field1 value1 [field2 value2 ]](https://www.runoob.com/redis/hashes-hmset.html)| 同时将多个 field-value (域-值)对设置到哈希表 key 中。 |
| 11 | [HSET key field value](https://www.runoob.com/redis/hashes-hset.html)| 将哈希表 key 中的字段 field 的值设为 value 。 |
| 12 | [HSETNX key field value](https://www.runoob.com/redis/hashes-hsetnx.html)| 只有在字段 field 不存在时，设置哈希表字段的值。 |
| 13 | [HVALS key](https://www.runoob.com/redis/hashes-hvals.html)| 获取哈希表中所有值。 |
| 14 | [HSCAN key cursor [MATCH pattern] [COUNT count]](https://www.runoob.com/redis/hashes-hscan.html)| 迭代哈希表中的键值对。 |
