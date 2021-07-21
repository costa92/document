map 



1、一个是字符串数组 [] string, 说明需要处理的数据是一个 字符串

2、一个是一个函数func(s string) string 或 func(s string) int

代码：



```go
func MapStrToStr(arr []string,fn func(s string) string) []string{
  var newArray [] string{}
  for _,it := range arr {
    newArray = append(newArray,fn(it))
  }
  return newArray
}

func MapStrToInt(arr []string ,fn func(s string)int)[]int{
  var newArray []int{}
  for _,it := range arr {  // 循环处理数据
    newArray = append(newArray,func(it))  // 元素添加到 newArray 数组中
  }
  return newArray
}
```

整个 Map 函数的运行逻辑都很相似，函数体都是在遍历第一个参数的数组，然后，调用第二个参数的函数，把它的值组合成另一个数组返回。

测试代码：

```go
func main(){
  var list = []string{"Hao","Chen","MegaEase"}
  x := MapStrToStr(list,func(s string) string{
    return string.ToUpper(s)  // 转化大写功能
  })
  fmt.Printf("%v\n",y)
 //["HAO", "CHEN", "MEGAEASE"]
  
  y := MapStrToInt(list,func(s string) int{
    return len(s)  // 计算长度
  })
  fmt.Printf("%v\n",y)
  // [3,4,8]
}
```

## 简单版 Generic Map

简单版 Generic Map  ,现在go 暂时不支持泛型，大约在2022年2月（1.18版本）落地，所有现在使用泛型只能通过interface() + reflect 来实现，interface{} 可以理解为 C 中的  void* 、Java 中的 Object, reflect 是go的反射机制包，作用是在运行时检查类型。

不做任何类型检查的泛型的 Map 函数

```go
func Map(data interface{},fn interface{}) [] interface {
  vfn := reflect.ValueOf(fn)  // 通过reflect.ValueOf() 获得 interface{} 的值， vfn
  vdata := reflect.ValueOf(data) // 通过reflect.ValueOf() 获取data
  result := make([]interface{},vdata.Len())
  
  for i:=0;i< vdata.Len();i++{
    // 通过 vfn.Call() 方法调用函数，通过[]refelct.Value{vdata.Index(i)} 获得数据
    result[i] = vfn.Call([]reflect.Value{vdata.Index(i)})[0].Interface()
  }
  return result
}
```

测试代码：

```go
square := func(x int) int{
  return x * x
}

nums := []int{1,2,3,4}
squared_arr := Map(nums,square)
fmt.Println(squared_arr)
// [1,4,9,16]


upcase := func(s string) string{
  return strings.ToUpper(s)
}

strs :=[]string{"Hao","Che","MegaEase"}
upstrs := Map(str,upcase);
fmt.Println(upstrs)
// [HAO,CHEN,MEGAeASE]
```

## 健壮版的 Generic Map

对于这种用interface{} 的“过度泛型”，就需要我们自己来做类型检查。来看一个有类型检查的 Map 代码：

```go

import (
	"fmt"
	"reflect"
)

func TransForm(slice,function interface{}) (interface{},bool) {
	return transform(slice,function,true)
}


func TransformPlace(slice,function interface{}) (interface{},bool){
	return transform(slice,function,true)
}

func transform(slice interface{}, function interface{}, inPlace bool) (interface{},bool) {
	// check the `slice` type is Slice
	sliceInType := reflect.ValueOf(slice)
	if sliceInType.Kind() != reflect.Slice {
		panic("transform:not slice")
	}

	// check the function signature
	fn := reflect.ValueOf(function)
	elemType := sliceInType.Type().Elem()
	if !verifyFuncSignature(fn,elemType,nil) {
		panic("transform:function must be of type func(" + sliceInType.Type().Elem().String()+") outputElemType")
	}

	// 重新赋值 注意可能出现内存问题
	sliceOutType := sliceInType
	if !inPlace {  // 判断是否替换原来的地址
		sliceOutType = reflect.MakeSlice(reflect.SliceOf(fn.Type()).Out(0),sliceInType.Len(),sliceOutType.Len())
	}
	for i := 0; i < sliceInType.Len();i++ {
		sliceOutType.Index(i).Set(fn.Call([]reflect.Value{sliceInType.Index(i)})[0])
	}
	return sliceOutType.Interface(), false
}

func verifyFuncSignature(fn reflect.Value, types ...reflect.Type) bool {
	// Check it is a function
	if fn.Kind() != reflect.Func {
		return false
	}
	fmt.Println(types)
	// NumIn() - returns a function type's input parameter count
	// NumOut - returns a function type's output parameter count.
	if (fn.Type().NumIn() != len(types) - 1) || (fn.Type().NumOut() != 1) {
		return false
	}
	fmt.Println(fn.Type().NumOut())
	// In() - returns the type of a function type's i'th input parameter.
	for i:=0; i < len(types)-1;i++{
		if fn.Type().In(i) != types[i]{
			return false
		}
	}

	// Out() - returns the type of a function type's i'th output parameter.

	outType := types[len(types)-1]
	if outType != nil && fn.Type().Out(0) != outType{
		return false
	}
	return true
}
```

代码中没有使用 Map 函数，因为和数据结构有含义冲突的问题，所以使用Transform，这个来源于 C++ STL 库中的命名。

有两个版本的函数，一个是返回一个全新的数组 Transform()，一个是“就地完成” TransformInPlace()。

在主函数中，用 Kind() 方法检查了数据类型是不是 Slice，函数类型是不是 Func。

检查函数的参数和返回类型是通过 verifyFuncSignature() 来完成的：NumIn()用来检查函数的“入参”；NumOut() ：用来检查函数的“返回值”。

如果需要新生成一个 Slice，会使用 reflect.MakeSlice() 来完成。

reflect.MakeSlice 方法的源码:

```go
func MakeSlice(typ Type, len, cap int) Value {
	if typ.Kind() != Slice {
		panic("reflect.MakeSlice of non-slice type")
	}
	if len < 0 {
		panic("reflect.MakeSlice: negative len")
	}
	if cap < 0 {
		panic("reflect.MakeSlice: negative cap")
	}
	if len > cap {
		panic("reflect.MakeSlice: len > cap")
	}

	s := unsafeheader.Slice{Data: unsafe_NewArray(typ.Elem().(*rtype), cap), Len: len, Cap: cap}
	return Value{typ.(*rtype), unsafe.Pointer(&s), flagIndir | flag(Slice)}
}
```

unsafeheader 构造体的文件在 internal/unsafeheader的目录下

```go

// Slice is the runtime representation of a slice.
// It cannot be used safely or portably and its representation may
// change in a later release.
//
// Unlike reflect.SliceHeader, its Data field is sufficient to guarantee the
// data it references will not be garbage collected.
type Slice struct {
	Data unsafe.Pointer
	Len  int
	Cap  int
}
```

代码测试：

可以用于字符串数组：

```go

list := []string{"1","2","3","4","5","6"}
result := Transform(list,func(a string) string{
  return a + a + a
})
fmt.Pirntln(result)

// {"111","222","333","444","555","666"}
```

可以用于整型数组：

```go
list := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
result := Transform(list,func(a int) int{
  	return a*3
})

fmt.Pirntln()
//{3, 6, 9, 12, 15, 18, 21, 24, 27}
```

