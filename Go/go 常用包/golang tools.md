
##  [deadcode](https://pkg.go.dev/golang.org/x/tools/cmd/deadcode) 
### 检查没有用的代码或过时的代码

安装 
```sh
go install golang.org/x/tools/cmd/deadcode@latest
```

测试
```go
func main() {
 var g Greeter
 g = Helloer{}
 g.Greet()
}
 
type Greeter interface{ Greet() }
 
type Helloer struct{}
type Goodbyer struct{}
 
var _ Greeter = Helloer{}
var _ Greeter = Goodbyer{}
 
func (Helloer) Greet()  { hello() }
func (Goodbyer) Greet() { goodbye() }
 
func hello()   { fmt.Println("你好，golang！") }
func goodbye() { fmt.Println("再见，golang！") }
```

运行
```sh
$ go run main.go
你好，golang！
```

使用 deadcode 检查
```sh
$ deadcode .
main.go:20:17: unreachable func: Goodbyer.Greet
main.go:23:6: unreachable func: goodbye
```

检测结果是 goodbye 函数和 Goodbyer.Greet 方法都无法访问。也就是这个代码本身的存在是没有运行意义的。


同时也可以借助命令的子选项 `-whylive`，让检测工具给我们解释为什么 `greet.hello` 函数是有效的。

如下解释结果：
```sh
$ deadcode -whylive=example.com/greet.hello .
                  example.com/greet.main
dynamic@L0008 --> example.com/greet.Helloer.Greet
 static@L0019 --> example.com/greet.hello
                   example.com/greet.main
  ...
```

> 注意: deadcode 工具，必须要包含 main 函数。言外之意就是其检测链路是从 main 函数开始的。


## [staticcheck](https://staticcheck.dev/docs/getting-started/)

### Staticcheck 是一款先进的 Go 编程语言代码检查工具。它使用静态分析来查找错误和性能问题、提供简化并强制执行样式规则。

安装
```sh
go install honnef.co/go/tools/cmd/staticcheck@latest
```

测试 
1. 未使用的
```go
package main

import "fmt"

func main() {
    var unusedVar int
    fmt.Println("Hello, World!")
}

```

运行 `staticcheck`

```sh
staticcheck ./...
main.go:6:6: unusedVar is unused (SA4006)
```

原因: 在 Go 中，未使用的局部变量会触发编译器警告，甚至可能导致编译失败。`staticcheck` 强调这些问题是为了帮助开发者保持代码的简洁，并避免潜在的错误或不必要的开销。

1.  for 循环没有停止
```go
for { fmt.Println("Infinite loop") }
```

提示的错误:
```sh
infinite for loop with no break condition (SA5001)
```

原因:  虽然无限循环在某些情况下是有意的设计，但 `staticcheck` 会提示开发者确认是否这是预期行为，以免忘记退出条件或意外进入死循环。

3. 格式化字符串中的多余参数（`printf` 调用问题)

```go
fmt.Printf("Hello, %s!", "world", "extra")
```

```sh
Printf format %s reads arg #1, but extra argument is provided (SA5009)
```
**原因**：在 `Printf` 格式化字符串中，存在一个未使用的参数。`staticcheck` 能够检测到这种情况，帮助避免误用或潜在的输出错误。

4. 无效的类型断言（`SA5007`）
```go
var i interface{} = "string"
_ = i.(int)  // invalid type assertion

```

```sh
invalid type assertion: interface{} cannot have dynamic type string (SA5007)
```
**原因**：类型断言的类型与实际动态类型不符，这会导致 panic。如果类型不确定，应该使用双值断言形式 `i.(int)`，并检查断言是否成功。


##  [gocyclo](https://github.com/fzipp/gocyclo)

Gocyclo 计算 Go 源代码中函数的[圈复杂度。](https://en.wikipedia.org/wiki/Cyclomatic_complexity)

安装:

```sh
 go install github.com/fzipp/gocyclo/cmd/gocyclo@latest
 ```

用法:
```sh
Calculate cyclomatic complexities of Go functions.
Usage:
    gocyclo [flags] <Go file or directory> ...

Flags:
    -over N               show functions with complexity > N only and
                          return exit code 1 if the set is non-empty
    -top N                show the top N most complex functions only
    -avg, -avg-short      show the average complexity over all functions;
                          the short option prints the value without a label
    -ignore REGEX         exclude files matching the given regular expression

The output fields for each line are:
<complexity> <package> <function> <file:line:column>
```

**圈复杂度解释**

- **数值越高，复杂度越大**。一般来说，函数的复杂度应该保持在合理范围（如小于 10），高于这个值的函数可能需要优化或重构。
- 每个 `if`、`for`、`switch`、`case`、`goto`、`return` 和其他控制流结构会增加函数的圈复杂度。

测试

```go
package main

import "fmt"

func calculateSum(nums []int) int {
    sum := 0
    for _, num := range nums {
        if num > 0 {
            sum += num
        } else if num < 0 {
            sum -= num
        } else {
            continue
        }
    }
    return sum
}

func main() {
    nums := []int{1, -2, 3, 0, -5}
    fmt.Println(calculateSum(nums))
}

```

运行 `gocyclo` 后，可能得到以下结果：

```sh
6 main.go:4:1: calculateSum
```

这里 `calculateSum` 的圈复杂度为 6，因为它有多个分支控制流（`if-else` 和 `for` 循环）。

如何减少圈复杂度

较高的圈复杂度通常意味着函数包含过多的逻辑分支或循环，使其难以维护。可以通过以下方法降低复杂度：

- **拆分大函数**：将复杂的函数逻辑拆分为多个小函数。
- **减少嵌套层次**：避免过深的嵌套，使用早返回（early return）来简化代码逻辑。
- **合理使用设计模式**：例如策略模式或状态模式，减少条件分支。

 过滤输出

如果你只想查看复杂度高于某个阈值的函数，可以使用 `-over` 标志。例如，过滤出复杂度大于 10 的函数：

```sh
gocyclo -over 10 ./...
```


## [goreplay](https://github.com/buger/goreplay)

GoReplay 是一个开源网络监控工具，它可以记录您的实时流量并将其用于跟踪、负载测试、监控和详细分析

最基本的设置是`sudo ./gor --input-raw :8000 --output-stdout`像 tcpdump 一样运行的程序。如果您已经有测试环境，则可以通过运行以下命令开始重放：`sudo ./gor --input-raw :8000 --output-http http://staging.env`。


## [depth](https://github.com/KyleBanks/depth)

**depth**是一个命令行应用程序，用于可视化特定软件包或一组软件包的依赖关系树，适用于您自己的软件包、第三方库或 Go 标准库。只需使用软件包名称执行 **depth即可进行可视化**


## [gomodifytags](https://github.com/fatih/gomodifytags)

用于修改/更新结构体中字段标签的 Go 工具。`gomodifytags`可轻松更新、添加或删除结构体字段中的标签。您可以轻松添加新标签、更新现有标签（例如附加新键，即：`db`、`xml`等）或删除现有标签。它还允许您添加和删除标签选项。它旨在由编辑器使用，但也有从终端运行它的模式。阅读下面的使用部分了解更多信息。

安装 
```sh
go install github.com/fatih/gomodifytags@latest
```

测试
```go
package main

type Server struct {
	Name        string
	Port        int
	EnableLogs  bool
	BaseDomain  string
	Credentials struct {
		Username string
		Password string
	}
}
```

我们必须先传递一个文件。为此，我们可以使用该`-file`标志：

```sh
 gomodifytags -file demo.go
-line, -offset, -struct or -all is not passed
```
- `-struct`：这接受结构名称。即：`-struct Server`。名称应为有效的类型名称。`-struct`标志选择整个结构，因此它将对所有字段进行操作。
- `-field`：接受字段名称。即：`-field Address`。用于选择某个字段。名称应为有效字段名称。`-struct`标志是必需的。
- `-offset`：这接受文件的字节偏移量。对于编辑器传递光标下的位置很有用。即：`-offset 548`。偏移量必须在有效结构内。`-offset`选择整个结构。如果您需要更细粒度的选项，请参阅`-line`
- `-line`：这接受一个字符串，该字符串定义应更改字段的行。即：`-line 4`或`-line 5,8`
- `-all`：这是一个布尔值。该`-all`标志选择给定文件的所有结构。

**添加标签和选项**

```sh
$ gomodifytags -file demo.go -struct Server -add-tags json
```

```go
package main

type Server struct {
	Name        string `json:"name"`
	Port        int    `json:"port"`
	EnableLogs  bool   `json:"enable_logs"`
	BaseDomain  string `json:"base_domain"`
	Credentials struct {
		Username string `json:"username"`
		Password string `json:"password"`
	} `json:"credentials"`
}
```

默认情况下，更改将打印到标准输出，可用于在进行破坏性更改之前试运行更改。如果您想永久更改它，请传递`-w`(write) 标志。

```sh
$ gomodifytags -file demo.go -struct Server -add-tags json -w
```

## [gops](https://github.com/google/gops)

gops 是一个用于列出和诊断系统上当前运行的 Go 进程的命令。

安装
```sh
$ go install github.com/google/gops@latest
```

测试
```sh
$ gops

288447  288433 gopls         go1.23.0 /home/hellotalk/code/go/bin/gopls
288433  287917 gopls         go1.23.0 /home/hellotalk/code/go/bin/gopls
361195  3713   telepresence  go1.18.3 /usr/local/bin/telepresence
370150  370132 esbuild       go1.20.7 /mnt/data/code/web/cms/cms-web/node_modules/.pnpm/@esbuild+linux-x64@0.18.20/node_modules/@esbuild/linux-x64/bin/esbuild
810006  472134 dlv           go1.23.0 /home/hellotalk/code/go/bin/dlv
810644  810006 cms-api       go1.23.0 /home/hellotalk/code/go/src/code.hellotalk.com/cms/cms-api/bin/cms-api
1100297 591864 gops          go1.23.0 /home/hellotalk/code/go/bin/gops

```

输出显示：

- PID
- 父级PID
- 项目名称
- 用于构建程序的 Go 版本
- 相关程序的位置
####   `gops <pid> [duration]`

```sh
$ gops 810006

parent PID:     472134
threads:        18
memory usage:   1.021%
cpu usage:      0.153%
username:       hellotalk
cmd+args:       /home/hellotalk/code/go/bin/dlv dap --client-addr=:41369
elapsed time:   03:12:48
local/remote:   127.0.0.1:47956 <-> 127.0.0.1:41369 (ESTABLISHED)
local/remote:   :0 <-> :0 (NONE)
````

如果按照 的预期格式指定了可选持续时间  [time.ParseDuration](https://pkg.go.dev/time#ParseDuration)，则还会报告给定时间段内的 CPU 使用率：

```sh
gops 810006 2s

parent PID:     472134
threads:        18
memory usage:   1.021%
cpu usage:      0.153%
cpu usage (2s): 0.000%
username:       hellotalk
cmd+args:       /home/hellotalk/code/go/bin/dlv dap --client-addr=:41369
elapsed time:   03:14:21
local/remote:   127.0.0.1:47956 <-> 127.0.0.1:41369 (ESTABLISHED)
local/remote:   :0 <-> :0 (NONE)

```

#### gops tree  (要显示包含所有正在运行的 Go 进程的进程树，请运行以下命令：)

```sh
 gops tree
...
├── 3713
│   └── 361195 (telepresence) {go1.18.3}
├── 287917
│   └── 288433 (gopls) {go1.23.0}
│       └── 288447 (gopls) {go1.23.0}
├── 370132
│   └── 370150 (esbuild) {go1.20.7}
├── 472134
│   └── 810006 (dlv) {go1.23.0}
│       └── 810644 (cms-api) {go1.23.0}
└── 591864
    └── 1112980 (gops) {go1.23.0}


```

####  `gops stack (<pid>|<addr>)`

为了从目标程序打印当前堆栈跟踪，请运行以下命令： 
在本地模式下，使用进程的 PID 作为目标；在远程模式下，目标是一个`host:port`组合。

```sh
gops stack 
```

注意: 需要在程序代码中添加,否则是不生效的
```go
"github.com/google/gops/agent"
```

```go
package main

import (
	"log"
	"time"

	"github.com/google/gops/agent"
)

func main() {
	if err := agent.Listen(agent.Options{
        Addr: "listen tcp address",  // 你想监听的地址，不填的话系统会自动分配一个端口给它用。
    }); err != nil {
		log.Fatal(err)
	}
	time.Sleep(time.Hour)
}
```

该功能与 [https://golang.org/pkg/net/http/pprof/](https://golang.org/pkg/net/http/pprof/) 一样的