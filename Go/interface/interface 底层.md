# interface 的底层

golang的interface接口，表示一组方法集合；实现方法的实例可以存储在接口类型的变量；有一种特殊的接口类型就是空接口类型interface{}，对于带有方法的接口类型和不带任何方法的 `interface{}` 类型，底层实现是不一样的。

Go的interface源码在Golang源码的`runtime`目录中的runtime2.go 文件中。

Go 的 interface 是由两种类型来实现的：iface 与 aface

查看 iface 与 eface 两个类型的源码：

空interface{} 的底层实现是 eface 构造体

```go
type eface struct {  // 16 bytes
_type *_type //指向下边的结构，出来底层类型外 还包含其他信息
data  unsafe.Pointer //指向底层数据(具体值）
}
```
<img src="https://file.longqiuhong.com/markdown/eface_all.png" alt="eface的结构" style="zoom:50%;" />

带有方法集合的接口实现底层 iface 构造体 

```go
type iface struct {  // 16 bytes
	tab  *itab  // 指向下边的结果，出来底层类型外，还包括其他的信息
	data unsafe.Pointer // 指向底层数据（具体值）
}
```

![itab 结构类型](https://file.longqiuhong.com/markdown/eface-20210515192921770.png )

itab 的构造体的

```go
// layout of Itab known to compilers
// allocated in non-garbage-collected memory
// Needs to be in sync with
// ../cmd/compile/internal/gc/reflect.go:/^func.dumptabs.
type itab struct {  // 32 bytes
	inter *interfacetype  // 此属性用于定位到具体interface
	_type *_type  // 此属性用于定位到具体interface
 // 用于类型转换，转换成具体类型需要判断目标类型和接口的底层类型是否一致
	hash  uint32 // copy of _type.hash. Used for type switches.  
	_     [4]byte
  //是一个指针数组，每个指针指向具体类型 _type 实现的具体方法
	fun   [1]uintptr // variable sized. fun[0]==0 means _type does not implement inter.
}
```

![itab 结构类型](https://i6448038.github.io/img/interface/iface_itable.png)    

属性`interfacetype`类似于`_type`，其作用就是interface的公共描述，类似的还有`maptype`、`arraytype`、`chantype`…其都是各个结构的公共描述，可以理解为一种外在的表现信息。`interfacetype`源码如下

```go
type imethod struct {
	name nameOff
	ityp typeOff
}

type interfacetype struct {
	typ     _type
	pkgpath name
	mhdr    []imethod
}
```

![interfacetype 的结构](https://i6448038.github.io/img/interface/iface_all.png)



 这两个接口共同的字段  _type  , _type 类型源码：

```go
// Needs to be in sync with ../cmd/link/internal/ld/decodesym.go:/^func.commonsize,
// ../cmd/compile/internal/gc/reflect.go:/^func.dcommontype and
// ../reflect/type.go:/^type.rtype.
// ../internal/reflectlite/type.go:/^type.rtype.
type _type struct {
	size       uintptr
	ptrdata    uintptr // size of memory prefix holding all pointers
	hash       uint32
	tflag      tflag
	align      uint8
	fieldAlign uint8
	kind       uint8
	// function for comparing objects of this type
	// (ptr to object A, ptr to object B) -> ==?
	equal func(unsafe.Pointer, unsafe.Pointer) bool
	// gcdata stores the GC type data for the garbage collector.
	// If the KindGCProg bit is set in kind, gcdata is a GC program.
	// Otherwise it is a ptrmask bitmap. See mbitmap.go for details.
	gcdata    *byte
	str       nameOff
	ptrToThis typeOff
}
//_type这个结构体是golang定义数据类型要用的
```

对于含有方法的interface赋值后的内部结构是怎样的呢？
一下代码运行后

```go
package main

import (
	"fmt"
	"strconv"
)

type Binary uint64
func (i Binary) String() string {
	return strconv.FormatUint(i.Get(), 10)
}

func (i Binary) Get() uint64 {
	return uint64(i)
}

func main() {
	b := Binary(200)
	any := fmt.Stringer(b)
	fmt.Println(any)
}
```

首先，要知道代码运行结果为:200。
其次，了解到`fmt.Stringer`是一个包含`String`方法的接口。

```go
type Stringer interface {
	String() string
}
```

最后，赋值后接口`Stringer`的内部结构为：
![在这里插入图片描述](https://i6448038.github.io/img/interface/iface_fuzhi.png)

对于将不同类型转化成itable中`type(Binary)`的方法，是运行时的`convT2I`方法，在`runtime`包中。

