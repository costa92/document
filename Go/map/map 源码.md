# map源码分析

基于  Go1.15.8 

1.数据结构及内存管理

hasmap 定义为 src/runtime/map.go 

```go
// A header for a Go map.
type hmap struct {
	count     int // 元素数量
	flags     uint8  // 状态标识
	B         uint8  // log_2 of # of buckets (can hold up to loadFactor * 2^B items)
	noverflow uint16 // approximate number of overflow buckets; see incrnoverflow for details
	hash0     uint32 // hash seed

	buckets    unsafe.Pointer // // bucket数组指针，数组的大小为2^B
	oldbuckets unsafe.Pointer // previous bucket array of half the size, non-nil only when growing
	nevacuate  uintptr   // progress counter for evacuation (buckets less than this have been evacuated)

	extra *mapextra // optional fields
}

```

