## context

```go
type Context interface {
    // Done 返回一个只读 channel, 该 channel 在 context 被取消或超时时关闭
    Done() <- chan struck{}
    // Err 返回 Context 结束时的出错信息
    Err() error
    
    // 如果 Context 被设置了超时， Deadline 将会返回超时时限
    Deadline() (deadline time.Time,ok bool)
    
    // Value 返回关联到相关 Key 上的值，或者nil
    Value(key interface{}) interface{}
}
```

Done() 方法返回一个只读的 channel, 当 Context 被主动取消或者超时自动取消时，该Context 所有派生 Context的 done channel 都被 close。所有子过程通过改字段收到 close 信号后，应该立即中断执行，释放资源然后返回。

Err() 在上述 channel 被 close 前会返回 nil，在被close后会该Context被关闭的信息，error 类型，只有两种，被取消或超时

```go
var Canceled = errors.New("context canceled")
var DeadlineExceeded error = deadlineExceededError{}
```

Deadline 如果本 Context 被设置了时限，则该函数返回 ok=true 和对应的到期时间点，否则，返回  ok = false 和 nil

Value() 返回绑定在该 Context 链上的给定的 Key的值，如果没有，则返回nil。注意不要用在函数中传参，其本意在于共享一些横跨整个Context生命周期范围的值。Key 可以是任何可比较类型。为了防止 Key 冲突，最好将 key的类型定义为 非导出类型，然后为其定义访问器。看一个通过Context共享用户信息的例子：

```go
package user

// User 是要存于 Context 中的 Value 类型
type User struct {...}

// key 定义未了非导出类型，以避免和其他 package 中的 key 的冲突
type key int

// userKey 是 Context 中用来关联 user.User 的 key，是非导出变量
// 客户端需要用 user.NewContext 和 user.FromContext 构建包含
// user 的 Context 和 从 Context 中提取相应 user
var userKey key

// NewContext 返回一个带有用户值 u 的 context
func NewContext(ctx context.Context,u *User) context.Context {
    return context.WithValue(ctx,userKey,u)
}
// FromContext 从 Context 中提取 user 如果有的话
func FromContext(ctx context.Context) (*User,bool) {
    u,ok := ctx.Value(userKey).(*User)
    return u,ok
}
```

### Context 派生

Context 设计之妙在于可以从已有 Context 进行树形派生，以管理一组过程的生命周期。Context 实例是不可的， 可以通过 context 包提供的三种方法：WithCancel、WithTimeout 和 WithValue 来进行派生并附加一些属性 (可取消、时限、键值), 以构造一组树形组织的 Context

当根 Context 结束时, 所有由其派生出的 Context 也会被一并取消。也就是说，父 Context 的生命周期涵盖所有的子 Context 的生命周期

context.Backgroud() 通常用作根节点，它不会超时，不能取消

```go
// Background 返回一个空 Context。 它不能取消，没有时限，没有附加键值 Background 通常用在 main 函数 、 init 函数、test人口 ，作为某个耗时过程的 Context
func Background() Context
```

