# new 函数

```go
func new(Type) *Type
```

new函数只接收一个参数，这个参数是一个类型，分配内存后，返回一个指向改类型内存地址的指针，请注意同时把分配的内存置为零，就是类型的零值。

示例：

```go
package main
import(
    "fmt"
)
func main(){
    var i *int
    i=new(int)
    *i=10
    fmt.Println(*i)
}
```
new函数返回的永远是类型指针，指向分配类型的内存地址

**错误代码**：

如果没有给 i 分配空间，看看运行的结果：
```go
package main
import(
    "fmt"
)
func main(){
    var i *int
    *i = 10
    fmt.Println(*i)
}
```
运行结果是0还是10?。以上全错，运行的时候会painc，原因如下：

```go
panic: runtime error: invalid memory address or nil pointer dereference
```

* 从运行的结果看出，对于引用类型的变量，需要声明它，而且而且还需要分配内存空间，否则赋值是无法有空间保存的，
* 对于值的类型的声明不需要，是因为已经默认分配好
* 分配内存，Go提供了两个方式，分别是**new**和**make**
