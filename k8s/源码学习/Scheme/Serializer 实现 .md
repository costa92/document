# Serializer 代码实现

k8s.io/apimachinery/pkg/runtime/schema/codec.go  文件定义 codec

```go
type coder struct {
    Encoder  // 编码 
	Decoder  // 解码
}
```

在 k8s.io/apimachinery/pkg/runtime/interface.go 文件定义了 编码与解码的 interface

```go
type Identifier string 
// Encoder writes objects to a serialized form
// 编码 interface
type Encoder interface {
	Encode(obj Object, w io.Writer) error
	Identifier() Identifier
}

// 解码 interface
type Decoder interface {
	Decode(data []byte, defaults *schema.GroupVersionKind, into Object) (Object, *schema.GroupVersionKind, error)
}

```

在文件还定义了：

```go
type Serializer interface {
   Encoder
   Decoder
}

// Codec is a Serializer that deals with the details of versioning objects. It offers the same
// interface as Serializer, so this is a marker to consumers that care about the version of the objects
// they receive.
type Codec Serializer
```



定义了接口，选择需要了解怎么样来实现