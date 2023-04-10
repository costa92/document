# Serializer 代码实现

1. 定义接口

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



```go
type NegotiatedSerializer interface {
	// SupportedMediaTypes is the media types supported for reading and writing single objects.
	SupportedMediaTypes() []SerializerInfo  // 方法实现对不同数据格式资源的支持，例如常见的 json，ymal，protobuf 等协议

	// EncoderForVersion returns an encoder that ensures objects being written to the provided
	// serializer are in the provided group version.
	EncoderForVersion(serializer Encoder, gv GroupVersioner) Encoder
	// DecoderToVersion returns a decoder that ensures objects being read by the provided
	// serializer are in the provided group version by default.
	DecoderToVersion(serializer Decoder, gv GroupVersioner) Decoder
}

```

EncoderForVersion 和 DecoderForVersion 方法来得到相应的 Encoder 和 Decoder 来进行序列化和反序列化，这里面得到的 Encoder 和 Decoder 一般就是我们上面介绍的 codec 对象。

```go
// SerializerInfo contains information about a specific serialization format
type SerializerInfo struct {
	// MediaType is the value that represents this serializer over the wire.
	MediaType string
	// MediaTypeType is the first part of the MediaType ("application" in "application/json").
	MediaTypeType string
	// MediaTypeSubType is the second part of the MediaType ("json" in "application/json").
	MediaTypeSubType string
	// EncodesAsText indicates this serializer can be encoded to UTF-8 safely.
	EncodesAsText bool
	// Serializer is the individual object serializer for this media type.
	Serializer Serializer
	// PrettySerializer, if set, can serialize this object in a form biased towards
	// readability.
	PrettySerializer Serializer
	// StrictSerializer, if set, deserializes this object strictly,
	// erring on unknown fields.
	StrictSerializer Serializer
	// StreamSerializer, if set, describes the streaming serialization format
	// for this media type.
	StreamSerializer *StreamSerializerInfo
}
```



- serializerinfo 内部有关键成员 MediaType 来定义所支持的资源数据格式。
- serializerinfo 内部有关键成员 Serializer (别名为 Codec)来支持序列化和反序列化操作，同时 Serializer 也是 Encoder 和 Decoder 接口的组合，这个由上面 codec 相关源码可以看到

2. 实现接口

​		**codec factory 的创建**: 

定义在方法 NewCodecFactory() 内部调用  newSerializersForScheme() 方法来创建支持不同数据格式的 Serializer 对象，然后又调用 newCodecFactory() 方法来实现对象创建。

newSerializersForScheme() 方法主要是创建支持各种数据格式 (json, yaml, protobuf 等) 的 serializer 对象		

![图片](https://file.longqiuhong.com/uploads/picgo/640.png)

 k8s.io/apimachinery/pkg/runtime/serializer/codec_factory.go 实现了 newSerializersForScheme 

```go
func newSerializersForScheme(scheme *runtime.Scheme, mf json.MetaFactory, options CodecFactoryOptions) []serializerType {
   jsonSerializer := json.NewSerializerWithOptions(
      mf, scheme, scheme,
      json.SerializerOptions{Yaml: false, Pretty: false, Strict: options.Strict},
   )
   jsonSerializerType := serializerType{
      AcceptContentTypes: []string{runtime.ContentTypeJSON},
      ContentType:        runtime.ContentTypeJSON,
      FileExtensions:     []string{"json"},
      EncodesAsText:      true,
      Serializer:         jsonSerializer,

      Framer:           json.Framer,
      StreamSerializer: jsonSerializer,
   }
   if options.Pretty {
      jsonSerializerType.PrettySerializer = json.NewSerializerWithOptions(
         mf, scheme, scheme,
         json.SerializerOptions{Yaml: false, Pretty: true, Strict: options.Strict},
      )
   }

   strictJSONSerializer := json.NewSerializerWithOptions(
      mf, scheme, scheme,
      json.SerializerOptions{Yaml: false, Pretty: false, Strict: true},
   )
   jsonSerializerType.StrictSerializer = strictJSONSerializer

   yamlSerializer := json.NewSerializerWithOptions(
      mf, scheme, scheme,
      json.SerializerOptions{Yaml: true, Pretty: false, Strict: options.Strict},
   )
   strictYAMLSerializer := json.NewSerializerWithOptions(
      mf, scheme, scheme,
      json.SerializerOptions{Yaml: true, Pretty: false, Strict: true},
   )
   protoSerializer := protobuf.NewSerializer(scheme, scheme)
   protoRawSerializer := protobuf.NewRawSerializer(scheme, scheme)

   serializers := []serializerType{
      jsonSerializerType,
      {
         AcceptContentTypes: []string{runtime.ContentTypeYAML},
         ContentType:        runtime.ContentTypeYAML,
         FileExtensions:     []string{"yaml"},
         EncodesAsText:      true,
         Serializer:         yamlSerializer,
         StrictSerializer:   strictYAMLSerializer,
      },
      {
         AcceptContentTypes: []string{runtime.ContentTypeProtobuf},
         ContentType:        runtime.ContentTypeProtobuf,
         FileExtensions:     []string{"pb"},
         Serializer:         protoSerializer,
         // note, strict decoding is unsupported for protobuf,
         // fall back to regular serializing
         StrictSerializer: protoSerializer,

         Framer:           protobuf.LengthDelimitedFramer,
         StreamSerializer: protoRawSerializer,
      },
   }

   for _, fn := range serializerExtensions {
      if serializer, ok := fn(scheme); ok {
         serializers = append(serializers, serializer)
      }
   }
   return serializers
}
```