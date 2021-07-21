# Reduce



reduce  定义为接收一个函数作为累加器，数组中的每一个值（从左到右）开始缩减，最终计算一个值。

代码：

```go
func Reduce(arr []string,fn func(s string) int) int{
  sum :=0 
  for _,it :=range arr{
    sum += fn(it)
  }
  return sum
}

var list = []string{"Hao","Chen","MegaEase"}
x := Reduce(list,func(s string) int {
  return len(s)
})
fmt.Printf("%v\n",x)

```

