# json 常用包

### jsonvalue

https://github.com/Andrew-M-C/go.jsonvalue

### jsonparser 

适合动态和固定结构的json, 简单且易用, 维护成本低, 性能极好
https://github.com/buger/jsonparser


### ourjson

https://github.com/W1llyu/ourjson

官方的做法主要是一整个json放一起解析，需要提前定义好struct来承载 或者用一个map[string]interface来承载，获取value时转成相应的类型。

ourjson这个库有点类似java里面的JSONObject，下面是例子

```go
package main

import (
  "fmt"
  "github.com/W1llyu/ourjson"
)

func main() {
    defer func() {
        if err := recover(); err != nil {
            fmt.Println(err)
        }
    }()
    jsonStr := `{
        "user": {
            "name": "aa",
            "age": 10,
            "phone": "12222222222",
            "emails": [
                "aa@164.com",
                "aa@165.com"
            ],
            "address": [
                {
                    "number": "101",
                    "now_live": true
                },
                {
                    "number": "102",
                    "now_live": null
                }
            ],
            "account": {
                "balance": 999.9
            }
        }
    }
    `
    jsonObject, err := ourjson.ParseObject(jsonStr)
    fmt.Println(jsonObject, err)

    user := jsonObject.GetJsonObject("user")
    fmt.Println(user)

    name, err := user.GetString("name")
    fmt.Println(name, err)

    phone, err := user.GetInt64("phone")
    fmt.Println(phone, err)

    age, err := user.GetInt64("age")
    fmt.Println(age, err)

    account := user.GetJsonObject("account")
    fmt.Println(account)

    balance, err := account.GetFloat64("balance")
    fmt.Println(balance, err)

    email1, err := user.GetJsonArray("emails").GetString(0)
    fmt.Println(email1, err)

    address := user.GetJsonArray("address")
    fmt.Println(address)

    address1nowLive, err := user.GetJsonArray("address").GetJsonObject(0).GetBoolean("now_live")
    fmt.Println(address1nowLive, err)

    address2, err := address.Get(1)
    fmt.Println(address2, err)

    address2NowLive, err := address2.JsonObject().GetNullBoolean("now_live")
    fmt.Println(address2NowLive, err)
}
```