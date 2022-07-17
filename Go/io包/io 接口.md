#  io 接口

strings.Reader 类型主要用于读取字符串，它的指针类型实现的接口比较多
```go
io.Reader
io.ReaderAt
io.ByteReader
io.RuneReader
io.Seeker
io.ByteScanner
io.RuneScanner
io.WriterTo
```

在 io 包中，用于拷贝数据的函数：
```go
io.Copy
io.CopyBuffer
io.CopyN
```