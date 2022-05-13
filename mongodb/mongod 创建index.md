#  mongodb 创建索引

```mongodb
db.collection.createIndex({"你的字段": -1})
```

在字段age 上创建索引，1(升序)*;-1(降序),这里两种写法，应该都可以。建表时创建；*

```mongodb
db.users.ensureIndex({age:1}) 
```

已存在大量数据，后台执行创建，backgroud设为true即可

```mongodb
db.t3.ensureIndex({age:1} , {backgroud:true})
```



查看索引：

```mongodb
db.collection.getIndexes();
```

删除索引：

```mongodb
//删除你的collecton中的所有索引
db.你的collecton.dropIndexes()
//删除你的collecton中的firstname 索引
db.你的collecton.dropIndex({name: 1})
```

