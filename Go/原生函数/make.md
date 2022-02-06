#  make函数

* make 函数用于内存分配，但是与 new 函数不一样，make 函数只能用于channal(通道)、map(映射)、slice(切片)的内存创建
* make 函数的返回的类型就是这三个类型本身，而不是它们的指针类型，因为这个三种本身就是引用类型
* 因为这个三种是引用类型，所有必须的初始化，但不是置为零值

```go
func make(t type,size ...IntegerType) Type
```


查看例子：
```go
package main
import(
    "fmt"
)
func main(){
	chans := make(chan int, 10)
	fmt.Println("chans:", chans)

	fmt.Println("len(chans):", len(chans))

	fmt.Println("cap(chans):", cap(chans))

	maps := make(map[int]int, 10)

	fmt.Println("maps:", maps)
	fmt.Println("len(maps)", len(maps))


	slice := make([]int, 5)
	fmt.Println("len(slice):", len(slice))

	fmt.Println("cap(slice):", cap(slice))
}
```

**注意：** make 函数与new 函数对比

* 两者都是内存分配（堆上），但是make函数 只用于slice、map以及channl的初始化（非零值），new函数用于类型的内存分配，并内存置为零
* make函数返回的还是这个三个引用类型的本身，而new 函数返回的是指针
* new 不常用，通常都是采用短语来声明以及结构体的字面量来实现：

```go
i :=0  // 短语
u := user{} //结构体的字面量
```
* make 函数是无可代替的

### 切片slice

* Slice 和数组类似，也是表示一个有序元素，但是这个序列的长度可表,具有动态长度特性
* 切片(slice) 是对底层数组一个连续片段的引用，所有切片是一个引用类型
* 内部实现的数据结构通过指针引用底层数组，设定相关属性数据读写操作限定在指定的区域内
* 切片本身是一个只读对象，其工作机制类似数组指针的一种封装
* 多个切片可以指向同一个底层数组，实现了内存共享
* Slice 的数据结构定义：

```go
type slice struct{
    array unsafe.Pounter
    len int
    cap int
}
```

* 切片的结构体由是3部分构成，Pointer 是指向一个底层数组的指针，len 代表当前切片的长度，cap是当前切片的容量，cap 总是大于等于 len 的

**切片的使用**

* Go语言提供的内置函数 make() 可以用于灵活地创建数组切片
* 创建方式 make([]type,len,cap),其中，type 代表数组元素类型，len代表长度，cap代表容量
* 还有一种切片字面量方式创建切片

```go
// 创建一个初始元素长度为5的数组切片，元素初始值为0
mySlice := make([]int,5)
// 创建一个初始化长度为5的数组切片，元素初始值为0 并娱乐10个元素的存储空间
mySlice2 := make([]int,5,10)
// 切片字面量创建长度为5容量为5的切片，需要注意是 [] 里面不要写数组容量，因为如果写了个数以后就是数组，而不是切片
mySlice3 := []int{10,20,30,40,50}
```

**字典map**

* map 是一种数据结构，用于存储一系列无序的键值对，类似java的HasMap
* 通过散列表实现，使用两个数据结构来存储数据，一个数组用于选择桶的散列键的高八位值，可以区分每个键值属于哪个桶；另一个字节数组，用于存储键值对
* 创建方式make(map[keytype]valueType,cap),其中keyType表示键类型，valueType表示值类型，cap表示初始存储能力

```go
// 创建了一个键类型为 string、值类型为PersonInfo
myMap = make(map(string) PersonInfo)
// 也可以选择是否在创建时指定改map的初始存储能力，创建一个初始存储能力为100的map
myMap = mmake(map[string] PersonInfo,100)

// 创建并初始化map的代码：
myMap = map[string] PersonInfo{
    "1234":PersonInfo{"1","jakc","room1"}
}

```

**通道channel**
* Channel 提供了一种机制，它既可以同步两个并发执行的函数，又可以让这个两个函数通过相互传递特定类型的值来通信
* Channel 让我们更容易编写清晰、正确的并发程序
* Channel 有点类型Java中常用来做多线程间数据通信的阻塞队列BlockingQueue
* Channel 的组成是一个数据环形数组队列，用于存储消息元素 + 两个链表实现的 goroutine 阻塞队列，用于存储阻塞在recv 和 send 操作上的 goroutine + 互斥锁Mutex,用于各个属性变动的同步
* 内部实现了 make(创建)、send(发送)、receive（接收）、 close（关闭）四个方式管理Channel的生命周期
* 分为无缓存Channel和有缓存Channel，无缓存Channel 可以理解为容量为1的阻塞队列，有缓存Channel则是容量为 N (N > 1) 的阻塞队列
* 创建方式make(chan type, cap)，其中type表示通道数据类型，cap表示缓存容量

```go
//创建有缓存通道
ch := make(chan int, 10)
//创建无缓存通道
ch := make(chan int)
```

基本操作：
```go

//创建有缓存通道
ch := make(chan int,10)
// 发送数据到channel
ch<-

// 从channel 接收数据
x <-ch
// 另外一种接收数据
x = <- ch

// 关闭channel
close(ch)
```

Range 遍历

```go
ch := make(chan int,10)
// 发送数据到channel
ch <- 1
ch <- 2
ch <- 3

// 通过range遍历接收通道数据
for x := range ch{
    fmt.PrintLn(x)
}

```

