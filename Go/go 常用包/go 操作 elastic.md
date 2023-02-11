# elastic

elastic 是分布式搜索与分析引擎

### 版本

安装v7版本
```bash
go get github.com/olivere/elastic/v7
```

### 客户端连接

```go
func init(){
    client, err = elastic.NewClient(
		elastic.SetSniff(false), 				// SetSniff启用或禁用嗅探器（默认情况下启用）。
        elastic.SetURL("127.0.0.1:9090"), 		// URL地址
		elastic.SetBasicAuth("username", "password"),	// 账号密码
	)

	if err != nil {
		panic(err)
	}
}

```

### Ping检查集群中的节点

```go
client.Ping(host).Do(context.Background())
```

### 查看版本

```go
client.ElasticsearchVersion(host)
```

### 创建文档

a) 结构体形式

```go
type user struct {
	name string
	age int
}

func main(){
    u := user{
		name: "wunder",
		age: 18,
	}
	_, err := client.Index().
    	Index("index_name").  		// 索引名称
    	Id("id").					// 指定文档id
    	BodyJson(u).				// 可序列化JSON
    	Do(context.Background())
	
    if err != nil {
		panic(err)
	}
}

```

b) 字符串形式

```go
u := `{"name":"wunder", "age": 1}`
_, err := client.Index().
	Index("index_name").  		// 索引名称
	Id("id").					// 指定文档id
	BodyJson(u).				// 可序列化JSON
	Do(context.Background())

if err != nil {
	panic(err)
}

```

### 修改文档
elasticsearc的修改是可以直接用创建去替换，如果单字段修改可以使用以下修改：

```go
_, err := client.Update().
	Index("index_name").
	Id("id").
	Doc(map[string]interface{}{"name":"wunder"}). // 需要修改的字段值
	Do(context.Background())

if err != nil {
	panic(err)
}

```

### 批量操作

```go 
type user struct {
	id string
	name string
	age int
}
func main() {
	users := []user{
		{
			id: "1",
			name: "wunder",
			age: 18,
		},
		{
			id: "2",
			name: "sun",
			age: 20,
		},
	}
	bulkRequest := client.Bulk()   								//初始化新的BulkService。
	for _, u := range users {
		doc := elastic.NewBulkUpdateRequest().Id(u.id).Doc(u)  	// 创建一个更新请求
		bulkRequest = bulkRequest.Add(doc)						// 添加到批量操作
	}
	_, err := bulkRequest.Do(context.Background())
	if err != nil {
		panic(err)
	}

}

```

### 删除文档
注意：在实际开发中不建议直接删除文档，可以定义delete_time字段做逻辑删除。

```go
_, err := client.Delete().Index("index_name").
		Id("id").
		Do(context.Background())
if err != nil {
   panic(err)
}

```

### 查询操作

a) 通过id查询

```go
ret, err := client.Get().Index("index_name").
		Id("id").					// 文档id 
		Do(context.Background())
if err != nil {
	panic(err)
}
fmt.Printf("id:%s \n Source:%s ", ret.Id, string(ret.Source))

```

b) 条件查询

```go
var query elastic.Query

// match_all
query = elastic.NewMatchAllQuery()

// term
query = elastic.NewTermQuery("field_name", "field_value")

// terms
query = elastic.NewTermsQuery("field_name", "field_value")

// match
query = elastic.NewMatchQuery("field_name", "field_value")

// match_phrase
query = elastic.NewMatchPhraseQuery("field_name", "field_value")

// match_phrase_prefix
query = elastic.NewMatchPhrasePrefixQuery("field_name", "field_value")

//range Gt:大于; Lt:小于; Gte:大于等于; Lte:小于等
query = elastic.NewRangeQuery("field_name").Gte(1).Lte(2)

//regexp
query = elastic.NewRegexpQuery("field_name", "regexp_value")

_, err := client.Search().Index("index_name").Query(query).Do(context.Background())

if err != nil {
	panic(err)
}

```

c) 组合查询

```go
// 创建新的bool查询
query := elastic.NewBoolQuery()

// must
query.Must(elastic.NewTermQuery("name", "wunder"))

// should
query.Should(elastic.NewTermQuery("age", 18))

//must_not
query.MustNot(elastic.NewTermQuery("age", 20))

// 结果: {"bool":{"must":{"term":{"name":"wunder"}},"must_not":{"term":{"age":20}},"should":{"term":{"age":18}}}}

_, err := client.Search().Index("index_name").Query(query).Do(context.Background())
if err != nil {
	panic(err)
}

```

d) 其他筛选

```go
// 文档查询数量,默认为10。
client.Search().Index("index_name").Size(100).Do(context.Background())

//开始搜索的索引,默认为0。
client.Search().Index("index_name").From(100).Do(context.Background())

//排序顺序, true为降徐， false为升序
client.Search().Index("index_name").Sort("field_name", true).Do(context.Background())

// 还可以通过SortBy进行多个排序
sorts := []elastic.Sorter{
	elastic.NewFieldSort("field_name01").Asc(), // 升序
	elastic.NewFieldSort("field_name02").Desc(), // 降徐
}
client.Search().Index("index_name").SortBy(sorts...).Do(context.Background())

// 返回指定字段
includes:= []string{"name", "age"}
include := elastic.NewFetchSourceContext(true).Include(includes...)
client.Search().Index("index_name").FetchSourceContext(include).Do(context.Background())

//查询的总命中计数
client.Search().Index("index_name").TrackTotalHits(true).Do(context.Background())


```

e) 打印bool查询语句

```go
// 创建新的bool查询
query := elastic.NewBoolQuery()

query.Must(elastic.NewTermQuery("name", "wunder"))

source, _ := query.Source()
fmt.Println(source)
// 结果：map[bool:map[must:map[term:map[name:wunder]]]]
```