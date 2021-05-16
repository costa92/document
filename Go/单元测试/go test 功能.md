# 单元测试功能

1. 在目录下执行 **go test** 是测试目录所有以**XXX_test.go** 结尾的文件。
2. 测试单个方法 下面2种写法。

```go
　　go test -test.v -test.run="Test_Division_1" -test.count 5
   go test -v -run="Test_Division_1" -count 5
```

3. 查看帮助 **go test -help** 

测试代码：

​		add.go 文件

```go
package mytest

func Add(x int,y int) int{
  return x + y
}

```

​	 add_test.go 文件

```go
import (
	"fmt"
	"testing"
)

func TestAdd(t *testing.T){
  x: = 1
  y: = 3
  addResult := 4
  result := Add(x,y)
  if result == addResult{
    t.Log("add test passed")
  }else{
    t.Error("add test as expected")
  }
}
```

