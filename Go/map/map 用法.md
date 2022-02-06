# map 用法

```go
func main(){
    m := make(map[string]int,10)
    fmt.Println("len(m):",len(m))
    fmt.Println("cap(m)",cap(m))
} 
```
运行错误：

./hello.go:5: invalid argument m (type map[string]int) for cap


先来看一下go的内置函数cap与map:

cap: 返回的是数组切片分配的空间大小, 根本不能用于map

make: 用于slice，map，和channel的初始化要获取map的容量，可以用len函数。