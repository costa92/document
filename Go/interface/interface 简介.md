# interface 的简介

##  interface 是一种数据类型



```go
type data interface{
  Get() int
}
```

 **interface 是一种类型**，从它的定义可以看出来用了 type 关键字，更准确的说 interface 是一种**具有一组方法的类型**，这些方法定义了 interface 的行为。



go 允许不带任何方法的 interface ，这种类型的 interface 叫 **empty interface**。

**如果一个类型实现了一个 interface 中所有方法，我们说类型实现了该 interface**，所以所有类型都实现了 empty interface，因为任何一种类型至少实现了 0 个方法。go 没有显式的关键字用来实现 interface，只需要实现 interface 包含的方法即可。

## interface 变量存储的是实现者的值



```go
type Data interface{
  Get() int
  Set(int) 
}

type DataStruct struct{
  	Age int
}
func(d DataStruct) Get() int {
  return d.Age
}

func(d DataStruct) Set(age int) {
  d.Age = age
}


func f(data Data){  // 该方法定义的参数是 interface 类型，
  data.Set(10)
  fmt.Println(data.Get（）)
}

func main(){
  // DataStruct 是 data的实现体
  s := DataStruct{} 
  f(&s)
}
```



interface 的重要用途就体现在**函数 f 的参数中**，如果有多种类型实现了某个 interface，**这些类型的值都可以直接使用 interface 的变量存储**。

## 如何判断 interface 变量存储的是哪种类型

## 空的 interface



## interface 的实现者的 receiver 如何选择

