# semaphore  信号量

源代码:  golang.org/x/sync/semaphore

1. 创建

```go
// NewWeighted使用给定的值创建一个新的加权信号量
// 并发访问的最大组合权重。
func NewWeighted(n int64) *Weighted {
   w := &Weighted{size: n}
   return w
}
```

w 结构

```go
// Weighted provides a way to bound concurrent access to a resource.
// The callers can request access with a given weight.
// NewWeighted使用给定的值创建一个新的加权信号量
// 并发访问的最大组合权重。
type Weighted struct {
    size    int64  //权重总数量
    cur     int64 //当前权重数量
    mu      sync.Mutex //全局互斥锁
    waiters list.List //双向链表,存waiter
}
```

waiter 结构

```go
type waiter struct {
    n     int64 //需要权重数量
    ready chan<- struct{} // Closed when semaphore acquired. //通信channel ,无缓冲
}
```

Acquire 方法:

阻塞的获取指定权种的资源，如果没有空闲的资源，会进去休眠等待。

```go
// Acquire获取权重为n的信号量，阻塞直到资源可用或ctx完成。
// 成功时，返回nil。失败时返回 ctx.Err（）并保持信号量不变。
// 如果ctx已经完成，则Acquire仍然可以成功执行而不会阻塞
func (s *Weighted) Acquire(ctx context.Context, n int64) error {
    s.mu.Lock()
        // fast path, 如果有足够的资源，都不考虑ctx.Done的状态，将cur加上n就返回
    if s.size-s.cur >= n && s.waiters.Len() == 0 {
      s.cur += n
      s.mu.Unlock()
      return nil
    }
  
        // 如果是不可能完成的任务，请求的资源数大于能提供的最大的资源数
    if n > s.size {
      s.mu.Unlock()
            // 依赖ctx的状态返回，否则一直等待
      <-ctx.Done()
      return ctx.Err()
    }
  
        // 否则就需要把调用者加入到等待队列中
        // 创建了一个ready chan,以便被通知唤醒
    ready := make(chan struct{})
    	// 组装waiter
    w := waiter{n: n, ready: ready}
    // 插入waiters中
    elem := s.waiters.PushBack(w)
    s.mu.Unlock()
  

        // 等待
    select {
    case <-ctx.Done(): // context的Done被关闭
      err := ctx.Err()
      s.mu.Lock()
      select {
      case <-ready: // 如果被唤醒了，忽略ctx的状态
        err = nil
      default: 通知waiter
        isFront := s.waiters.Front() == elem
        s.waiters.Remove(elem)
        // 通知其它的waiters,检查是否有足够的资源
        if isFront && s.size > s.cur {
          s.notifyWaiters()
        }
      }
      s.mu.Unlock()
      return err
    case <-ready: // 被唤醒了
      return nil
    }
  }
```

#### TryAcquire

非阻塞地获取指定权重的资源，如果当前没有空闲资源，会直接返回`false`。

```go
// TryAcquire获取权重为n的信号量而不阻塞。
// 成功时返回true。 失败时，返回false并保持信号量不变。
func (s *Weighted) TryAcquire(n int64) bool {
	s.mu.Lock()
	success := s.size-s.cur >= n && s.waiters.Len() == 0
	if success {
		s.cur += n
	}
	s.mu.Unlock()
	return success
}

```



#### Release 方法

用于释放指定权重的资源，如果有`waiters`则尝试去一一唤醒`waiter`。

```go
// Release释放权值为n的信号量。
func (s *Weighted) Release(n int64) {
	s.mu.Lock()
	s.cur -= n
	// cur的范围在[0 - size]
	if s.cur < 0 {
		s.mu.Unlock()
		panic("semaphore: bad release")
	}
	s.notifyWaiters()
	s.mu.Unlock()
}

func (s *Weighted) notifyWaiters() {
	// 如果有阻塞的waiters，尝试去进行一一唤醒 
	// 唤醒的时候，先进先出，避免被资源比较大的waiter被饿死
	for {
		next := s.waiters.Front()
		// 已经没有waiter了
		if next == nil {
			break
		}

		w := next.Value.(waiter)
		// waiter需要的资源不足
		if s.size-s.cur < w.n {
			// 没有足够的令牌供下一个waiter使用。我们可以继续（尝试
			// 查找请求较小的waiter），但在负载下可能会导致
			// 饥饿的大型请求；相反，我们留下所有剩余的waiter阻塞
			//
			// 考虑一个用作读写锁的信号量，带有N个令牌，N个reader和一位writer
			// 每个reader都可以通过Acquire（1）获取读锁。
			// writer写入可以通过Acquire（N）获得写锁定，但不包括所有的reader。
			// 如果我们允许读者在队列中前进，writer将会饿死-总是有一个令牌可供每个读者。
			break
		}

		s.cur += w.n
		s.waiters.Remove(next)
		close(w.ready)
	}
}
```

notifyWaiters 方法

在`Acquire`和`Release`方法中都调用了`notifyWaiters`

```go
func (s *Weighted) notifyWaiters() {
 for {
  // 获取等待调用者队列中的队员
  next := s.waiters.Front()
  // 没有要通知的调用者了
  if next == nil {
   break // No more waiters blocked.
  }

  // 断言出waiter信息
  w := next.Value.(waiter)
  if s.size-s.cur < w.n {
   // 没有足够资源为下一个调用者使用时，继续阻塞该调用者，遵循先进先出的原则，
   // 避免需要资源数比较大的waiter被饿死
   //
   // 考虑一个场景，使用信号量作为读写锁，现有N个令牌，N个reader和一个writer
   // 每个reader都可以通过Acquire（1）获取读锁，writer写入可以通过Acquire（N）获得写锁定
   // 但不包括所有的reader，如果我们允许reader在队列中前进，writer将会饿死-总是有一个令牌可供每个reader
   break
  }

  // 获取资源
  s.cur += w.n
  // 从waiter列表中移除
  s.waiters.Remove(next)
  // 使用channel的close机制唤醒waiter
  close(w.ready)
 }
}
```

需要注意一个点：唤醒`waiter`采用先进先出的原则，避免需要资源数比较大的waiter被饿死。

