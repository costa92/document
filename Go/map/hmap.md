# hmap 类型变量  -- *runtime.hmap

hmap 类型是 map 类型的头部结构 (header), 它存储了后续 map 类型操作

go 源码
```go
type hmap struct {
	// Note: the format of the hmap is also encoded in cmd/compile/internal/gc/reflect.go.
	// Make sure this stays in sync with the compiler's definition.
	count     int // # live cells == size of map.  Must be first (used by len() builtin)
	flags     uint8
	B         uint8  // log_2 of # of buckets (can hold up to loadFactor * 2^B items)
	noverflow uint16 // approximate number of overflow buckets; see incrnoverflow for details
	hash0     uint32 // hash seed

	buckets    unsafe.Pointer // array of 2^B Buckets. may be nil if count==0.
	oldbuckets unsafe.Pointer // previous bucket array of half the size, non-nil only when growing
	nevacuate  uintptr        // progress counter for evacuation (buckets less than this have been evacuated)

	extra *mapextra // optional fields
}

```


| 字段 | 说明 |
|---|---|
| count | 当前 map 的元素个数。 对 map 类型变量运用len内置函数时，len函数返回的就是count 这个|
| flags | 当前 map 所处的状态标志。目前定义了四个状态值： iterator、oldterator、hashWriting 和 sameSizeGrow|
| B | B的值是 bucket 数量的以 2 为底的对数，也就是 2^B = bucket 数量 |
| noverflow | overflow bucket 的大约数量 |
| hash0 | 哈希函数的种子值 |
| buckets | 指向bucket 数组的指针  |
| oldbuckets| 在 map 扩容阶段指向前一个bucket的数组的指针 |
| nevacuate | 在map扩容阶段充当扩容进度计数器，所有下标号小于 nevacuate 的 bucket 都已经完成了数据排空和迁移操作 |
| extra | 可选字段，如果有 overflow bucker 存在，且key、value 都因不包含指针而被内联 （inline）的情况下，这个字段将存储所有指向 overflow bucket 的指针，保证 overflow bucket 是始终可用的 （不被Gc掉）|
