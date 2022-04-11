# golang 使用aggregate

参考文档： [传送门](https://pkg.go.dev/go.mongodb.org/mongo-driver/mongo#Collection.Aggregate)

### matcg 查询使用

mongodb 使用查询语句

```go
db.room_member.aggregate([
    {
        $match: {
            roomid:21062
        }
    }
])
```

查询的结果：

<img src="/home/hellotalk/snap/typora/57/.config/Typora/typora-user-images/image-20220411154719373.png" alt="image-20220411154719373" style="zoom:80%;" />



使用golang 实现

```go
var aggregateArr []bson.M
o1 := bson.M{"$match": bson.M{"roomid": 21062}}
aggregateArr = append(aggregateArr, o1)
data, err := collec.Aggregate(ctx, aggregateArr)
```

```go
aggregateArr := bson.D{
	{
        "$match", []bson.E{
			{"roomid", 21062},
		},
    },
}
data, err := collec.Aggregate(ctx, mongo.Pipeline{aggregateArr})
```

```go
aggregateArr := bson.D{
	{
        "$match", bson.D{
			{"roomid", 21062},
		},
    },
}
data, err := collec.Aggregate(ctx, mongo.Pipeline{aggregateArr})
```



多个参数执行语句

```go
db.getCollection("room_member").aggregate([{
    $match: {
        roomid: 21062,
		quitstat: 0
    }
}])
```



golang 执行

```go
var aggregateArr []bson.M
o1 := bson.M{"$match": bson.M{"roomid": 21062, "quitstat": 0}}
aggregateArr = append(aggregateArr, o1)
data, err := collec.Aggregate(ctx, aggregateArr)
```



```go
aggregateArr := bson.D{
	{
        "$match", []bson.E{
			{"roomid", 21062},
            	{"quitstat", 0},
		},
    },
}
data, err := collec.Aggregate(ctx, mongo.Pipeline{aggregateArr})
```



```go
aggregateArr := bson.D{
		{"$match", bson.D{
			{"roomid", 21062},
			{"quitstat", 0},
		},
		},
}
data, err := collec.Aggregate(ctx, mongo.Pipeline{aggregateArr})
```



###  match 与 group 联合使用

执行sql 

```sql

db.room_member.aggregate([
    {
        $match: {
            roomid:21062,
			quitstat:0
        }
    },
    {
        $group: {
            _id: "$roomid",
            "count": {
                $sum: 1
            }
        }
    }
]);



```

![image-20220411173841973](/home/hellotalk/snap/typora/57/.config/Typora/typora-user-images/image-20220411173841973.png)

go 实现

使用 数组

```go
var aggregate []bson.M
o1 := bson.M{"$match": bson.M{"roomid": bson.M{"$in": []int{21062}}, "quitstat": 0}}
aggregate = append(aggregate, o1)
o2 := bson.M{"$group": bson.M{"_id": "$roomid", "count": bson.M{"$sum": 1}}}
aggregate = append(aggregate, o2)
curr, err := collec.Aggregate(ctx, aggregate)
```

如果是查询单个 roomId

```go
var aggregate []bson.M
o1 := bson.M{"$match": bson.M{"roomid": 21062, "quitstat": 0}}
aggregate = append(aggregate, o1)
o2 := bson.M{"$group": bson.M{"_id": "$roomid", "count": bson.M{"$sum": 1}}}
aggregate = append(aggregate, o2)
curr, err := collec.Aggregate(ctx, aggregate)
```

使用 mongo.Pipeline

```go
	aggregate := mongo.Pipeline{
		{
			{"$match", bson.D{
				{"roomid", 21062},
				{"quitstat", 0},
			}},
		},
		{
			{"$group", bson.D{
				{"_id", "$roomid"},
				{"count", bson.D{
					{"$sum", 1},
				}},
			}},
		},
	}

	curr, err := collec.Aggregate(ctx, aggregate)
```

