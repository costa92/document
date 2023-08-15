# 获取当前时间
```go
 timeObj := time.Now()
```

获取当前时间戳

```go
currTime := time.Now().Unix()
```

获取前一天时间

```go
currTime := time.Now()
lastDay := currTime.AddDate(0,0,-1).Unix()
```

获取时间格式：

```go
format := time.Now().Format("2006-01-02 15:04:05") 
format := time.Now().Format("2006-01-02") 
```

时间戳转换成格式

```go
time.Unix(1691942400, 0).Format("2006-01-02 15:04:05")
```

