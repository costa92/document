# Redis key的操作



1、查看数据库中的key： key  pattern

​												| -> *: 匹配0个或多个字符

​												|->?: 匹配1个字符

​												|->[]: 匹配[]里面的1个字符



```sh
keys *: 查看数据库中所有的 key
keys k*: 查看数据库中所有以k 开头的 key
keys h*o: 查看数据库中所有以k 开头，以o结尾的key
keys h?o: 查看数据库中所有以k 开头，以o结尾的key，并且中间只有一个字符的key
keys h[abc]llo: 查看数据库中所有以 h 开头以llo 结尾，并且h后面只能取 abc 中的一个字符的 key
```

2、判断ket在数据库是是否存在：

​					exists key  如果存在，则返回1,如果不存在，则返回0

​					exists  key [key key ...]  返回值是存在的 key 的数量

```sh
exists k1
exists k1 k2 k3
```

3、移动指定key 到指定的数据库实例： move key index  

```sh
move k 1
```

4、查看指定 key 的剩余生存时间: ttl  key 	

​																	|->  如果key没有设置生存时间，返回 -1

​																	|-> 如果key 不存在，返回 -2

```sh
ttl k2
```

5、设置key的最大生存时间：expire key seconds

```sh
expire k1 20
```

6、查看指定key数据类型： type key

```sh
type k1
```

7、重命名 key: rename key newkey

```sh
rename k1 k2
```

8、删除指定的 key:  del key [ key key ...]  返回值是实际删除的数量

```sh
del k1 
del k1 k3
```



​	 

