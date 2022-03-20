## pprof 工具使用
### pprof

golang pprof是golang的可视化和性能分析的工具。其提供了可视化的web页面，火焰图等更直观的工具。

可以使用 go tool pprof 进行使用

```sh
go tool pprof
```

go tool pprof 来源于 google/pprof 项目，可以以如下方式安装。

```sh
go get -u github.com/google/pprof
```

### 安装Graphviz

如果使用 -http 选项指定需要web交互页面，则需要安装 dot 
```sh
Failed to execute dot. Is Graphviz installed?
exec: "dot": executable file not found in $PATH
```
ubuntu 上通过以下方式安装：
```sh
sudo apt install graphviz
```

mac上通过以下方式安装：
```sh
brew install graphviz
```

contos上通过以下方式安装：
```sh
yum install graphviz
```

其他系统下载 请访问https://graphviz.org/download/

### 启用功能

需要我们的程序开放了pprof web端点。一般建议的方式为,在需要使用的地方引用net/http/pprof包。

```go
import _ "net/http/pprof"
```

该方式会在默认的 http.DefaultServeMux 中插入debug pprof端点。

```go
// pprof.go:79
func init() {
    http.HandleFunc("/debug/pprof/", Index)
    http.HandleFunc("/debug/pprof/cmdline", Cmdline)
    http.HandleFunc("/debug/pprof/profile", Profile)
    http.HandleFunc("/debug/pprof/symbol", Symbol)
    http.HandleFunc("/debug/pprof/trace", Trace)
}
```

不过在一般的开发中不使用该方式，而是使用自定义的handler，如下。
```go
	m.HandleFunc("/debug/pprof/", pprof.Index)
	m.HandleFunc("/debug/pprof/cmdline", pprof.Cmdline)
	m.HandleFunc("/debug/pprof/profile", pprof.Profile)
	m.HandleFunc("/debug/pprof/symbol", pprof.Symbol)
	m.HandleFunc("/debug/pprof/trace", pprof.Trace)

	server := &http.Server{
		Addr:    "127.0.0.1:8000",
		Handler: m,
	}
	// 设置服务器监听请求端口
	server.ListenAndServe()
```

pprof包内调用runtime包中函数以获取各种运行时信息，其包含如下分析指标。

* allocs: 过去所有内存分配的样本
* block: 导致同步原语阻塞的堆栈跟踪
* cmdline: 当前程序的命令行调用，与/proc/中的 cmdline相同
* goroutine: 当前所有goroutine的堆栈跟踪
* heap: A活动对象的内存分配的采样。您可以指定gc GET参数以在获取堆样本之前运行GC。
* mutex: 竞争互斥体持有人的堆栈痕迹
* profile: CPU配置文件。您可以在秒GET参数中指定持续时间。获取概要文件后，使用go tool pprof命令调查概要文件。
* threadcreate: 导致创建新OS线程的堆栈跟踪
* trace: 当前程序执行的痕迹。您可以在秒GET参数中指定持续时间。获取跟踪文件后，请使用go工具trace命令调查跟踪。


## CPU性能分析

进行CPU性能分析直接用go tool pprof访问上面说的/debug/pprof/profile端点即可，等数据采集完会自动进入命令行交互模式。

```sh
go tool pprof http://localhost:8000/debug/pprof/profile
输出结果：
Fetching profile over HTTP from http://localhost:8000/debug/pprof/profile
Saved profile in /Users/costalong/pprof/pprof.samples.cpu.002.pb.gz
Type: cpu
Time: Feb 8, 2022 at 1:53pm (CST)
Duration: 30s, Total samples = 0
No samples were found with the default sample value type.
Try "sample_index" command to analyze different sample values.
Entering interactive mode (type "help" for commands, "o" for options)
(pprof)
```

默认采集时长是 30s，如果在 url 最后加上 ?seconds=60 参数可以调整采集数据的时间为 60s。

### 列出最耗时的地方

一个有用的命令是 topN，它列出最耗时间的地方：
```sh
(pprof) top10
130ms of 360ms total (36.11%)
Showing top 10 nodes out of 180 (cum >= 10ms)
      flat  flat%   sum%        cum   cum%
      20ms  5.56%  5.56%      100ms 27.78%  encoding/json.(*decodeState).object
      20ms  5.56% 11.11%       20ms  5.56%  runtime.(*mspan).refillAllocCache
      20ms  5.56% 16.67%       20ms  5.56%  runtime.futex
      10ms  2.78% 19.44%       10ms  2.78%  encoding/json.(*decodeState).literalStore
      10ms  2.78% 22.22%       10ms  2.78%  encoding/json.(*decodeState).scanWhile
      10ms  2.78% 25.00%       40ms 11.11%  encoding/json.checkValid
      10ms  2.78% 27.78%       10ms  2.78%  encoding/json.simpleLetterEqualFold
      10ms  2.78% 30.56%       10ms  2.78%  encoding/json.stateBeginValue
      10ms  2.78% 33.33%       10ms  2.78%  encoding/json.stateEndValue
      10ms  2.78% 36.11%       10ms  2.78%  encoding/json.stateInString
```

每一行表示一个函数的信息。前两列表示函数在 CPU 上运行的时间以及百分比；第三列是当前所有函数累加使用 CPU 的比例；第四列和第五列代表这个函数以及子函数运行所占用的时间和比例（也被称为累加值 cumulative），应该大于等于前两列的值；最后一列就是函数的名字。如果应用程序有性能问题，上面这些信息应该能告诉我们时间都花费在哪些函数的执行上。


参考链接： [Golang程序性能分析（一）pprof和go-torch](https://mp.weixin.qq.com/s?__biz=MzUzNTY5MzU2MA==&mid=2247486618&idx=1&sn=bb5e76e011ba99ebc2ffb8f9d3c00b89&scene=21#wechat_redirect)