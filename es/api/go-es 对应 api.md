# go-es 对应的 api

### 创建索引：

Uri: {index}

Method: put

例子：

```go
PUT /ml_test_topic
{
  "settings":{
     "index":{
       "number_of_shards":5,
       "number_of_replicas":1
     }
  },
  "mappings":{
	"dynamic": false,
	"properties": {
	      "text": {
	        "type": "text"
	      }
	    }
  }

}
```



go-es 对应的方法:

```go
func indexCreate(es *elasticsearch.Client)(*esapi.Response,error){
	res ,err:= es.Indices.Create("ml_test_topic1",
		es.Indices.Create.WithBody(strings.NewReader(`{
  "settings":{
     "index":{
       "number_of_shards":5,
       "number_of_replicas":1
     }
  },
"mappings":{
	"dynamic": false,
	"properties": {
	      "text": {
	        "type": "text"
	      }
	    }
}
}`)))
```

### 修改索引的信息

Uri : /{index}/_mappings  

method：PUT

例子：

```go
PUT /ml_test_topic/_mappings
{
    "properties": {
		  "title":{
			  "type": "text",
			   "analyzer": "keyword"
		  }
    }
}
```

go-es的方法：

```go
func indexPutMapping(es *elasticsearch.Client)(*esapi.Response,error){
	res,err := es.Indices.PutMapping(
		[]string{"ml_test_topic"},
		strings.NewReader(`{
		"properties": {
			"title":{
					"type": "text",
	     		"analyzer": "keyword"
			}
		}}`),
	)
	fmt.Println(res)
	return res,err
}
```

什么时 mapping ?

**Mapping**,就是对索引库中索引的字段名称及其数据类型进行定义，类似于mysql中的表结构信息。不过es的mapping比数据库灵活很多，它可以动态识别字段。一般不需要指定mapping都可以，因为es会自动根据数据格式识别它的类型，**如果你**需要对某些字段添加特殊属性（如：定义使用其它分词器、是否分词、是否存储等），**就必须手动添加mapping**



### 添加数据

Uri: {index}/_doc

例子：

```go
POST /ml_test_topic/_doc
{
  "topic_id":1,
  "title":"test",
    "test":"hello worlds"
}

// 也可以定义_id

POST /ml_test_topic/_doc/1
{
  "topic_id":1,
  "title":"test",
   "test":"hello worlds"
}
```



Go-es:

```go

func create(es *elasticsearch.Client)(*esapi.Response,error){
	res, err := es.Index("ml_test_topic",strings.NewReader(
		`{
			"topic_id":"12",
			"title":"name",
			"text":"hello worlds !"
			}`),
		es.Index.WithRefresh("true"),
		es.Index.WithPretty(),
	)

	return res,err
}
```

### 查询

Uri: {index}/_search

Mehod: GET

例子:

```go
GET /ml_test_topic/_search
{
  "query": {
      "term": {
            "topic_id":"12"
          }
  }
}
```

Go-es

```go
func getSearch(es *elasticsearch.Client) (*esapi.Response,error) {
   res, err := es.Search(
      es.Search.WithIndex("ml_test_topic"),
      es.Search.WithBody(strings.NewReader(`{
      "query": {
          "term": {
            "topic_id":"12"
          }
      }
   }`)),
      es.Search.WithPretty(),
   )
   return res,err

}
```



### 查询条件在更新

Uri: {index}/_update_by_query

Mehod: POST

例子

```go
POST /ml_test_topic/_update_by_query 
{
"script": {
   "source":"ctx._source.title=params.title;",  // params.title 对应的 params.title
            "params": {
                "title": "看看外面的世界真的很精彩11"  // 修改的字段名
            },
            "lang": "painless"
	  },
	  "query": {
	    	"term": {
				"topic_id":"12"
	    	}
	  	}
	}
```

Go-es

```go
func updateByQuery(es *elasticsearch.Client)(*esapi.Response,error){
	res, err := es.UpdateByQuery(
		[]string{"ml_test_topic"},
		es.UpdateByQuery.WithBody(strings.NewReader(`{
"script": {
   "source":"ctx._source.title=params.title;",
            "params": {
                "title": "看看外面的世界真的很精彩"
            },
            "lang": "painless"
	  },
	  "query": {
	    	"term": {
				"topic_id":"12"
	    	}
	  	}
	}`)),
	)
	fmt.Println(res, err)

	return res,err
}
```



###  根据id更新

Uri: {index}/_update_by_query

Mehod: POST

例子

```go
POST test/_update/CoSbzXoB8ZR3dFwFsBRH
{
  "script" : {
    "source": "ctx._source.test = params.test",
    "lang": "painless",
    "params" : {
     "test": "123231"
    }
  }
}
```



Go-es

```go
func update(es *elasticsearch.Client) (*esapi.Response,error) {
   
   res, err := es.Update("ml_test_topic", "CoSbzXoB8ZR3dFwFsBRH", strings.NewReader(`{
"script": {
       "source": "ctx._source.test = params.test",
       "lang": "painless",
       "params": {
         "test": "123231"
       }
     }
   }`), es.Update.WithPretty())


   return res,err
}
```



Uri: {index}/_doc

Mehod: PUT

```console
PUT ml_test_topic/_doc/CoSbzXoB8ZR3dFwFsBRH
{
   "test": "2323323232"
}
```



Go-es

```go
func updateById(es *elasticsearch.Client) (*esapi.Response,error)  {
   res, err := es.Index(
      "ml_test_topic",
      strings.NewReader(`{
         "test": "2323323232"
   }`),
      es.Index.WithDocumentID("CoSbzXoB8ZR3dFwFsBRH"),
      es.Index.WithPretty(),
   )
   return res, err
}
```



参考说明：https://www.elastic.co/guide/en/elasticsearch/reference/master/docs-update.html

其他例子：https://www.jianshu.com/p/075c0ed51053

源码文档：https://pkg.go.dev/github.com/elastic/go-elasticsearch/v7