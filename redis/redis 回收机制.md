#  redis 回收机制

Redis自己也设计了一套垃圾回收方案。可以让redis中的存储空间得到优化。

### 1.什么情况下数据会成为垃圾

当redis中的key的生命时间到了，不会立即删除，当碰到者两种情况会被删除

* 定期删除：每隔100ms看3个key，如果被redis扫描到，那么就被删除

* 惰性删除：当key过期了，客户端get了一次，redis发现该key已过期，于是删除。


### 2.redis采用什么样的回收策略

* volatile-lru：在内存不足时，Redis会再设置过了生存时间的key中干掉一个最近最少使用的key。
* allkeys-lru：在内存不足时，Redis会在全部的key中干掉一个最近最少使用的key。
* volatile-lfu：在内存不足时，Redis会再设置过了生存时间的key中干掉一个最近最少频次使用的key。
* allkeys-lfu：在内存不足时，Redis会再全部的key中干掉一个最近最少频次使用的key。
* volatile-random：在内存不足时，Redis会再设置过了生存时间的key中随机干掉一个。
* allkeys-random：在内存不足时，Redis会再全部的key中随机干掉一个。
* volatile-ttl：在内存不足时，Redis会再设置过了生存时间的key中干掉一个剩余生存时间最少的key。
* noeviction：（默认）在内存不足时，只读不能写.

### 3.redis具体怎么回收垃圾
redis会根据选择的回收策略对待回收的key集合进行取样，样本的范围大小决定了性能，

* 范围越大：越精确，性能越差；
* 范围越小：越不精确，性能越好。

```redis
maxmemory-samples 5 # 保持默认即可
```