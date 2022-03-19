# 常用的字符串拼接方法

1.最常用的方法肯定是 + 连接两个字符串。这与python类似，不过由于golang中的字符串是不可变的类型，因此用 + 连接会产生一个新的字符串对效率有影响。

```go
s1 := "字符串"
s2 := "拼接"
s3 := s1 + s2
fmt.Print(s3) //s3 = "打印字符串"
 ```

2.第二种方法使用sprintf函数，虽然不会像直接使用 + 那样产生临时字符串。但是效率也不高
```go
s1 := "字符串"
s2 := "拼接"
s3 := fmt.Sprintf("%s%s", s1, s2) //s3 = "打印字符串"
```


3.第三种方法是用Join函数，这里我们需要先引入strings包才能调用Join函数。Join函数会先根据字符串数组的内容，计算出一个拼接之后的长度，然后申请对应大小的内存，一个一个字符串填入，在已有一个数组的情况下，这种效率会很高，如果没有的话效率也不高。
```go
//需要先导入strings包
s1 := "字符串"
s2 := "拼接"
//定义一个字符串数组包含上述的字符串
var str []string = []string{s1, s2}
//调用Join函数
s3 := strings.Join(str, "")
fmt.Print(s3)
```
 

4.第四个方法是调用buffer.WriteString函数，这种方法的性能就要大大优于上面的了。
```go
//需要先导入bytes包
s1 := "字符串"
s2 := "拼接"
//定义Buffer类型
var bt bytes.Buffer
向bt中写入字符串
bt.WriteString(s1)
bt.WriteString(s2)
//获得拼接后的字符串
s3 := bt.String()
```
 

5.第5个方法是用buffer.Builder，这个方法和上面的差不多，不过官方建议用这个，使用方法和上面基本一样

```go
//需要先导入Strings包
s1 := "字符串"
s2 := "拼接"
var build strings.Builder
build.WriteString(s1)
build.WriteString(s2)
s3 := build.String()
```
结论
一般对于少量的字符串拼接可以直接用+来连接，不过最好的方法还是Builder。

```go
package main

import (
	"bytes"
	"fmt"
	"math/rand"
	"strings"
)

const letterBytes = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"

// randomString 随机获取字符串
func randomString(n int) string {
	b := make([]byte, n)
	for i := range b {
		b[i] = letterBytes[rand.Intn(len(letterBytes))]
	}
	return string(b)
}

// plusConcat 使用加
func plusConcat(n int, str string) string {
	s := ""
	for i := 0; i < n; i++ {
		s += str
	}

	return s
}

// sprintfConcat 使用 fmt.Sprintf
func sprintfConcat(n int, str string) string {
	s := ""
	for i := 0; i < n; i++ {
		s = fmt.Sprintf("%s%s", s, str)
	}
	return s
}

// builderConcat 使用 strings.Builder
func builderConcat(n int, str string) string {
	var builder strings.Builder
	builder.Grow(n * len(str))
	for i := 0; i < n; i++ {
		builder.WriteString(str)
	}
	return builder.String()
}

// bufferConcat 使用 bytes.Buffer
func bufferConcat(n int, s string) string {
	buf := new(bytes.Buffer)
	for i := 0; i < n; i++ {
		buf.WriteString(s)
	}
	return buf.String()
}

// 使用 []byte
func byteConcat(n int, str string) string {
	buf := make([]byte, 0)
	for i := 0; i < n; i++ {
		buf = append(buf, str...)
	}
	return string(buf)
}

func preByteConcat(n int, str string) string {
	buf := make([]byte, 0, n*len(str))
	for i := 0; i < n; i++ {
		buf = append(buf ,str...)
	}

	return string(buf)
}



func main() {
	str := randomString(4)

	//str = plusConcat(3,str)
	//str = sprintfConcat(3,str)
	//str = builderConcat(3, str)
	//str = bufferConcat(3, str)
	//str = byteConcat(3, str)
	str = preByteConcat(3, str)
	fmt.Println(str)
}

```