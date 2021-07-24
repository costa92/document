# 操作数据string的命令

1. 将 string 类型的数据设置到 redis 中:  set 键 值

```sh
set zsname zhangsan
set zsage  11
set totalRows 100
```

2.  从 redis 中获取string类型的数据: get 键

   ```sh
   get zsname
   get zsage
   get totalRows
   ```

3. 追加字符串： append  键  值

   ​								|-> 返回追加之后的字符串长度

   ​								|-> 如果key 不存在，则创建一个key 并且把value值设置为value

   ```sh
   set phone  123344
   append phone 233232
   ```

   

4. 获取字符串数据长度： strlen 键

   ​											|->如果键不存在返回0，

   ​											|-> 如果键不是字符串类型，返回错误信息

   ```sh
   strlen phone
   ```

5. 