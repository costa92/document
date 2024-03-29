# 连接mongodb

###　通过 Go 连接 mongodb

```go
package main

import (
	"context"
	"fmt"
	"log"

	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
)

func main() {
	// 设置客户端连接配置
	clientOptions := options.Client().ApplyURI("mongodb://localhost:27017")

	// 连接到MongoDB
	client, err := mongo.Connect(context.TODO(), clientOptions)
	if err != nil {
		log.Fatal(err)
	}

	// 检查连接
	err = client.Ping(context.TODO(), nil)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Connected to MongoDB!")
```

连接上MongoDB之后，可以通过下面的语句处理我们上面的q1mi数据库中的student数据集了：

```go
// 指定获取要操作的数据集
collection := client.Database("q1mi").Collection("student")
```

处理完任务之后可以通过下面的命令断开与MongoDB的连接：

```go
// 断开连接
err = client.Disconnect(context.TODO())
if err != nil {
	log.Fatal(err)
}
fmt.Println("Connection to MongoDB closed.")
```

### 连接池模式

```go
import (
	"context"
	"time"

	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
)

func ConnectToDB(uri, name string, timeout time.Duration, num uint64) (*mongo.Database, error) {
	ctx, cancel := context.WithTimeout(context.Background(), timeout)
	defer cancel()
	o := options.Client().ApplyURI(uri)
	o.SetMaxPoolSize(num)
	client, err := mongo.Connect(ctx, o)
	if err != nil {
		return nil, err
	}

	return client.Database(name), nil
}
```

### BSON

MongoDB中的JSON文档存储在名为BSON(二进制编码的JSON)的二进制表示中。与其他将JSON数据存储为简单字符串和数字的数据库不同，BSON编码扩展了JSON表示，使其包含额外的类型，如int、long、date、浮点数和decimal128。这使得应用程序更容易可靠地处理、排序和比较数据。

连接MongoDB的Go驱动程序中有两大类型表示BSON数据：`D`和`Raw`。

类型`D`家族被用来简洁地构建使用本地Go类型的BSON对象。这对于构造传递给MongoDB的命令特别有用。`D`家族包括四类:

- D：一个BSON文档。这种类型应该在顺序重要的情况下使用，比如MongoDB命令。
- M：一张无序的map。它和D是一样的，只是它不保持顺序。
- A：一个BSON数组。
- E：D里面的一个元素。

要使用BSON，需要先导入下面的包:

```go
import "go.mongodb.org/mongo-driver/bson"
```

下面是一个使用D类型构建的过滤器文档的例子，它可以用来查找name字段与’张三’或’李四’匹配的文档:

```go
bson.D{{
	"name",
	bson.D{{
		"$in",
		bson.A{"张三", "李四"},
	}},
}}
```

`Raw`类型家族用于验证字节切片。你还可以使用`Lookup()`从原始类型检索单个元素。如果你不想要将BSON反序列化成另一种类型的开销，那么这是非常有用的。这个教程我们将只使用D类型。

### CRUD

定义一个`Studet`类型如下：

```go
type Student struct {
	Name string
	Age int
}
```

创建一些`Student`类型的值，准备插入到数据库中：

```go
s1 := Student{"小红", 12}
s2 := Student{"小兰", 10}
s3 := Student{"小黄", 11}
```

#### 插入文档

使用`collection.InsertOne()`方法插入一条文档记录：

```go
insertResult, err := collection.InsertOne(context.TODO(), s1)
if err != nil {
	log.Fatal(err)
}

fmt.Println("Inserted a single document: ", insertResult.InsertedI
```

使用`collection.InsertMany()`方法插入多条文档记录：

```go
students := []interface{}{s2, s3}
insertManyResult, err := collection.InsertMany(context.TODO(), students)
if err != nil {
	log.Fatal(err)
}
fmt.Println("Inserted multiple documents: ", insertManyResult.InsertedIDs)
```

#### 更新文档

`updateone()`方法允许你更新单个文档。它需要一个筛选器文档来匹配数据库中的文档，并需要一个更新文档来描述更新操作。你可以使用`bson.D`类型来构建筛选文档和更新文档:

```go
filter := bson.D{{"name", "小兰"}}

update := bson.D{
	{"$inc", bson.D{
		{"age", 1},
	}},
}
```

接下来，就可以通过下面的语句找到小兰，给他增加一岁了：

```go
updateResult, err := collection.UpdateOne(context.TODO(), filter, update)
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Matched %v documents and updated %v documents.\n", updateResult.MatchedCount, updateResult.ModifiedCount)
```

#### 查找文档

要找到一个文档，你需要一个filter文档，以及一个指向可以将结果解码为其值的指针。要查找单个文档，使用`collection.FindOne()`。这个方法返回一个可以解码为值的结果。

我们使用上面定义过的那个filter来查找姓名为’小兰’的文档。

```go
// 创建一个Student变量用来接收查询的结果
var result Student
err = collection.FindOne(context.TODO(), filter).Decode(&result)
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Found a single document: %+v\n", result)
```

要查找多个文档，请使用`collection.Find()`。此方法返回一个游标。游标提供了一个文档流，你可以通过它一次迭代和解码一个文档。当游标用完之后，应该关闭游标。下面的示例将使用`options`包设置一个限制以便只返回两个文档。

```go
// 查询多个
// 将选项传递给Find()
findOptions := options.Find()
findOptions.SetLimit(2)

// 定义一个切片用来存储查询结果
var results []*Student

// 把bson.D{{}}作为一个filter来匹配所有文档
cur, err := collection.Find(context.TODO(), bson.D{{}}, findOptions)
if err != nil {
	log.Fatal(err)
}

// 查找多个文档返回一个光标
// 遍历游标允许我们一次解码一个文档
for cur.Next(context.TODO()) {
	// 创建一个值，将单个文档解码为该值
	var elem Student
	err := cur.Decode(&elem)
	if err != nil {
		log.Fatal(err)
	}
	results = append(results, &elem)
}

if err := cur.Err(); err != nil {
	log.Fatal(err)
}

// 完成后关闭游标
cur.Close(context.TODO())
fmt.Printf("Found multiple documents (array of pointers): %#v\n", results)
```

### 简单条件

```go
//单个条件 =
func equal() {
	query := bson.M{"name": "Python程序设计开发宝典"}
	searchAll(query)
}

//单个条件 >
func gt() {
	query := bson.M{"price": bson.M{"$gt": 40.0}}
	searchAll(query)
}

//单个条件 <
func lt() {
	query := bson.M{"price": bson.M{"$lt": 40.0}}
	searchAll(query)
}

//单个条件 >=
func gte() {
	query := bson.M{"price": bson.M{"$gte": 45}}
	searchAll(query)
}

//单个条件 <=
func lte() {
	query := bson.M{"price": bson.M{"$lte": 36}}
	searchAll(query)
}

//单个条件 !=
func ne() {
	query := bson.M{"press": bson.M{"$ne": "清华大学出版社"}}
	searchAll(query)
}
```

### 多合条件查询

```go
/ and
//select * from table where price<=50 and press='清华大学出版社'
func and() {
	query := bson.M{"price": bson.M{"$lte": 50}, "press": "清华大学出版社"}
	searchAll(query)
}

// or
//select * from table where press='高等教育出版社' or press='清华大学出版社'
func or() {
	query := bson.M{"$or": []bson.M{bson.M{"press": "高等教育出版社"}, bson.M{"name": "Python程序设计开发宝典"}}}
	searchAll(query)
}

// not
// not条件只能用在正则表达式中
func not() {
	query := bson.M{"press": bson.M{"$not": bson.RegEx{Pattern: "^清华", Options: "i"}}}
	searchAll(query)
}


// 单个key的or查询可以使用 in 或 nin
func in() {
	query := bson.M{"press": bson.M{"$in": []string{"清华大学出版社", "机械工业出版社"}}}
	searchAll(query)
}

func nin() {
	query := bson.M{"press": bson.M{"$nin": []string{"清华大学出版社", "机械工业出版社"}}}
	searchAll(query)
}
```

### 正则查询，字符串模糊查询

```go
//正则查询
//$regex操作符的使用
//
//$regex操作符中的option选项可以改变正则匹配的默认行为，它包括i, m, x以及s四个选项，其含义如下
//
//i 忽略大小写，{<field>{$regex/pattern/i}}，设置i选项后，模式中的字母会进行大小写不敏感匹配。
//m 多行匹配模式，{<field>{$regex/pattern/,$options:'m'}，m选项会更改^和$元字符的默认行为，分别使用与行的开头和结尾匹配，而不是与输入字符串的开头和结尾匹配。
//x 忽略非转义的空白字符，{<field>:{$regex:/pattern/,$options:'m'}，设置x选项后，正则表达式中的非转义的空白字符将被忽略，同时井号(#)被解释为注释的开头注，只能显式位于option选项中。
//s 单行匹配模式{<field>:{$regex:/pattern/,$options:'s'}，设置s选项后，会改变模式中的点号(.)元字符的默认行为，它会匹配所有字符，包括换行符(\n)，只能显式位于option选项中。
//
//使用$regex操作符时，需要注意下面几个问题:
//
//i，m，x，s可以组合使用，例如:{name:{$regex:/j*k/,$options:"si"}}
//在设置索引的字段上进行正则匹配可以提高查询速度，而且当正则表达式使用的是前缀表达式时，查询速度会进一步提高，例如:{name:{$regex: /^joe/}

//字符串模糊查询  开头包含
func beginWith() {
	query := bson.M{"name": bson.M{"$regex": bson.RegEx{Pattern: "^高等", Options: "i"}}}
	searchAll(query)
}

//模糊查询 包含
func contains() {
	//query := bson.M{"name": bson.M{"$regex": "开发", "$options": "$i"}}
	query := bson.M{"name": bson.M{"$regex": bson.RegEx{Pattern: "开发", Options: "i"}}}
	searchAll(query)
}

//模糊查询 结尾包含
func endWith() {
	query := bson.M{"name": bson.M{"$regex": bson.RegEx{Pattern: "指南$", Options: "i"}}}
	searchAll(query)
}

```

### 数组查询

```go
//数组查询，数组中的元素可能是单个值数据，也可能是子文档
//针对单个值数据
//满足数组中单个值
func arrayMatchSingle() {
	query := bson.M{"tags": "编程"}
	searchAll(query)
}

//同时满足所有条件，不要求顺序
func arrayMatchAll() {
	query := bson.M{"tags": bson.M{"$all": []string{"程序设计", "编程", "python"}}}
	searchAll(query)
}

//查询特定长度
func arrayMatchSize() {
	query := bson.M{"tags": bson.M{"$size": 4}}
	searchAll(query)
}

//满足特定索引下条件
//数组索引从0开始，我们匹配第二项就用tags.1作为键
func arrayMatchIndex() {
	query := bson.M{"tags.1": "编程"}
	searchAll(query)
}

//精确查找，数量，顺序都要满足
func arrayMatch() {
	query := bson.M{"tags": []string{"数学", "大学数学", "高等数学"}}
	searchAll(query)
}

//针对与数组中的子文档
//满足单个价值
func subDocMatchSingle() {
	query := bson.M{"author.name": "纪涵"}
	searchAll(query)
}

//elementMath
func subDocMatchElement() {
	query := bson.M{"author": bson.M{"$elemMatch": bson.M{"name": "谢希仁", "sex": "男"}}}
	searchAll(query)
}

```



### 分页查询

使用Skip函数和Limit函数

```go
func (q *Query) Skip(n int) *Query
func (q *Query) Limit(n int) *Query
```

```go
func findPage() {
    db := getDB()
 
    c := db.C("user")
 
    type User struct {
        Id   bson.ObjectId `bson:"_id,omitempty"`
        Name string        "bson:`name`"
        Age  int           "bson:`age`"
    }
    var users []User
    // 表示从偏移位置为2的地方开始取两条记录
    err := c.Find(nil).Sort("-age").Skip(2).Limit(2).All(&users)
    if err != nil {
        panic(err)
    }
    fmt.Println(users)
    // output:
    // [{ObjectIdHex("56fdce98189df8759fd61e5d") Anny 20} ...]
    // ...
}
```



#### 删除文档

最后，可以使用`collection.DeleteOne()`或`collection.DeleteMany()`删除文档。如果你传递`bson.D{{}}`作为过滤器参数，它将匹配数据集中的所有文档。还可以使用`collection. drop()`删除整个数据集。

```go
// 删除名字是小黄的那个
deleteResult1, err := collection.DeleteOne(context.TODO(), bson.D{{"name","小黄"}})
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Deleted %v documents in the trainers collection\n", deleteResult1.DeletedCount)
// 删除所有
deleteResult2, err := collection.DeleteMany(context.TODO(), bson.D{{}})
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Deleted %v documents in the trainers collection\n", deleteResult2.DeletedCount)
```

**参考**：[官方文档](https://godoc.org/go.mongodb.org/mongo-driver)。
