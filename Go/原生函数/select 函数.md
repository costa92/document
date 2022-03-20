# select 函数

一个 select 语句用来选择哪个 case 中的发送或接收操作可以被立即执行。它类似于 switch 语句，但是它的case 涉及到 channel 有关的I/O 操作

换一种说法，select 就是用来监听和channel 有关的IO操作，当IO操作发生时，触发相应的动作

```go
select { // 不停的在这里检测
    case <- chan1: // 检测有没有数据可以读
    // 如果channl 成功读到数据，则进行该 case 处理
    case chan2 <- 1: // 检测有没有可以写
    // 如果成功向 chan2 写入数据，则进行该case 处理
    
    // 如果没有default ，那么在以上两个条件都不成立的情况下，就会在此阻塞 // 一般default 会不写在里面 select 
    default:
    // 如果以上都没有复活条件，那么则镜像default 处理流程
}

```
在一个 select 语句中,Go会按顺序从头到尾评估每一个发送和接收的语句。

如果其中的任何一个语句可以继续执行（即没有被阻塞），那么久从哪些可以执行的语句中任意选择一条来使用。

如果没有 任意一条语句可以执行 （即所有的通道都被阻塞），那么有两种洪可能情况：

1. 如果给出了 default 语句，那么就会执行 default 的流程，同时程序的执行会从 select 语句中恢复。

2. 如果没有default 语句，那么select 语句将会被堵塞，直到至少有一个case 可以进行执行下去

**官方文档**

Execution of a "select" statement proceeds in several steps:

1. For all the cases in the statement, the channel operands of receive operations and the channel and right-hand-side expressions of send statements are evaluated exactly once, in source order, upon entering the "select" statement. The result is a set of channels to receive from or send to, and the corresponding values to send. Any side effects in that evaluation will occur irrespective of which (if any) communication operation is selected to proceed. Expressions on the left-hand side of a RecvStmt with a short variable declaration or assignment are not yet evaluated.
所有channel表达式都会被求值、所有被发送的表达式都会被求值。求值顺序：自上而下、从左到右.
结果是选择一个发送或接收的channel，无论选择哪一个case进行操作，表达式都会被执行。RecvStmt左侧短变量声明或赋值未被评估。

2. If one or more of the communications can proceed, a single one that can proceed is chosen via a uniform pseudo-random selection. Otherwise, if there is a default case, that case is chosen. If there is no default case, the "select" statement blocks until at least one of the communications can proceed.
如果有一个或多个IO操作可以完成，则Go运行时系统会随机的选择一个执行，否则的话，如果有default分支，则执行default分支语句，如果连default都没有，则select语句会一直阻塞，直到至少有一个IO操作可以进行.

3. Unless the selected case is the default case, the respective communication operation is executed.
除非所选择的情况是默认情况，否则执行相应的通信操作。

4.I f the selected case is a RecvStmt with a short variable declaration or an assignment, the left-hand side expressions are evaluated and the received value (or values) are assigned.
如果所选case是具有短变量声明或赋值的RecvStmt，则评估左侧表达式并分配接收值（或多个值）。

5.The statement list of the selected case is executed.
执行所选case中的语句

文档链接： [https://golang.org/ref/spec#Select_statements](https://golang.org/ref/spec#Select_statements)

示例1 如果有一个或多个IO 操作可以完成，则Go运行时系统会随机的选择一个执行；否则的话，如果有default 分支，则执行到 default 分支语句，如果没有default，则select 语句会一直堵塞，直到至少有一个IO操作可以进行

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	start := time.Now()
	c := make(chan interface{})
	ch1 := make(chan int)
	ch2 := make(chan int)

	go func() {
		time.Sleep(4 * time.Second)
		close(c)
	}()

	go func() {
		time.Sleep(3 * time.Second)
		ch1 <- 3
	}()

	go func() {
		time.Sleep(3 * time.Second)
		ch2 <- 5
	}()

	fmt.Println("Blocking on read ...")
	select {
		case <-c :
			fmt.Printf("Unblocked %v later .\n",time.Since(start))
		case <-ch1:
			fmt.Printf("ch1 case...\n")
		case <-ch2:
			fmt.Printf("ch2 case...\n")
		default:
		fmt.Printf("default go...\n")
	}
}
```

运行上述代码，由于当前时间还未到3s。所以，目前程序会走default。

修改上述代码，将default注释:

这时，select语句会阻塞，直到监测到一个可以执行的IO操作为止。这里，先会执行完睡眠3s的gorountine,此时两个channel都满足条件，这时系统会随机选择一个case继续操作。
```sh
q1 > go run main.go
Blocking on read ...
ch2 case...

```

继续修改代码，将ch1 和 ch2 的gorountine休眠时间改为5s：
```go
go func() {
        time.Sleep(5*time.Second)
        ch1 <- 3
    }()
go func() {
        time.Sleep(5*time.Second)
        ch2 <- 3
    }()
    
```

此时会先执行到上面的gorountine，select执行的就是c的case。

示例2:  所有channel表达式都会被求值、所有被发送的表达式都会被求值。求值顺序：自上而下、从左到右.
```go
package main

import "fmt"

var ch1 chan int
var ch2 chan int
var chs = []chan int{ch1, ch2}
var numbers = []int{1,2,3,4,5}
func main() {
	select {
	case getChan(0) <- getNumber(2):
		fmt.Println("1st case is selected.")
	case getChan(1) <- getNumber(3):
		fmt.Println("2nd case is selected.")
	default:
		fmt.Println("default!.")
	}
}

func getNumber(i int) int  {
	fmt.Printf("numbers[%d] \n",i)
	return numbers[i]
}

func getChan(i int) chan int {
	fmt.Printf("chs[%d]\n",i)
	return chs[i]
}

```

此时，select语句走的是default操作。但是这时每个case的表达式都会被执行。以case1为例：
```go
case getChan(0) <- getNumber(2):
// ch1 <- 3 ，会 block住，因为无缓存的channel 是 synchronous 的，send 和 receive 必须同时满足 
```


示例3: break关键字结束select

```go
package main

import "fmt"

func main() {
	ch1 := make(chan int ,1)
	ch2 := make(chan int ,1)
	
	ch1 <- 3
	ch2 <- 5
	
	select{
	case <- ch1:
		fmt.Println("ch1 selected .")
		break
		fmt.Println("ch1 selected after break")
	case <- ch2:
		fmt.Println("ch2 selected.")
		fmt.Println("ch2 selected without break")
	}

}
```
很明显，ch1和ch2两个通道都可以读取到值，所以系统会随机选择一个case执行。我们发现选择执行ch1的case时，由于有break关键字只执行了一句：

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	ch1 := make(chan int ,1)
	ch2 := make(chan int ,1)
	
	ch1 <- 3
	ch2 <- 5

	time.Sleep(1 *  time.Second)
	select{
	case <- ch1:
		fmt.Println("ch1 selected .")
		break
		fmt.Println("ch1 selected after break")
	case <- ch2:
		fmt.Println("ch2 selected.")
		fmt.Println("ch2 selected without break")
	}

}
```

