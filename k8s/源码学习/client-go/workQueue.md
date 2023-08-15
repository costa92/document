# workQueue 源码

k8s 有三个队列：

queue.go (普通队列)

delaying_queue.go （延迟队列）

rate_limiting_queue.go  (限流队列)

## 普通队列(queue.go)

定义Queue的接口在queue.go 的文件中

````go
type Interface interface {
	Add(item interface{})   // 添加队列元素
	Len() int  // 获取元素的数量
	Get() (item interface{}, shutdown bool) // 第一个获取一个元素，第二个方法是否 channel 中类似，标记队列是否关闭
	Done(item interface{})  // 标记元素已经处理完成
	ShutDown()  // 关闭队列
	ShutDownWithDrain() // 关闭队列，但等待队列中元素处理我
	ShuttingDown() bool // 标记当前channel是否正在关闭
}
````

实现 Interface 接口的类为 Type, 下面代码定义了 Type的结构体：

```go
type Type struct {
	queue []t  // 定义元素的处理顺序，里面所有元素在 dirty 集合中应该都有，而不能出现在processing集合
	dirty set  // 标记所有需要被处理的元素
	processing set // 当前正在被处理的元素，当处理完成后，需要检查该元素是否在dirty集合中，如果在则添加到 queue 队列中

	cond *sync.Cond  

	shuttingDown bool
	drain        bool

	metrics queueMetrics

	unfinishedWorkUpdatePeriod time.Duration
	clock                      clock.WithTicker
}
```

