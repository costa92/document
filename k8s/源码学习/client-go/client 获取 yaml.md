# clinet 获取 yaml 文件

### 在 clinet-go 项目获取 yaml 代码文件再 tools/clientcmd/api/latest/latest.go 文件

代码如下：

```go
package latest

import (
	"k8s.io/apimachinery/pkg/runtime"
	"k8s.io/apimachinery/pkg/runtime/schema"
	"k8s.io/apimachinery/pkg/runtime/serializer/json"
	"k8s.io/apimachinery/pkg/runtime/serializer/versioning"
	utilruntime "k8s.io/apimachinery/pkg/util/runtime"
	"k8s.io/client-go/tools/clientcmd/api"
	"k8s.io/client-go/tools/clientcmd/api/v1"
)

const Version = "v1"

var ExternalVersion = schema.GroupVersion{Group: "", Version: "v1"}

var (
	Codec  runtime.Codec  // 外部调用使用
	Scheme *runtime.Scheme
)
func init() {
  // Scheme 用于定义 API 对象之间的关系,每个 k8s API 对象都具有一个相应的结构体类型，而 Scheme 定义了这些结构体类型之间的映射关系
	Scheme = runtime.NewScheme()
  // runtime.Must
	utilruntime.Must(api.AddToScheme(Scheme))  // 添加到旧版本
	utilruntime.Must(v1.AddToScheme(Scheme)) // 添加到新版本
	yamlSerializer := json.NewYAMLSerializer(json.DefaultMetaFactory, Scheme, Scheme)
	Codec = versioning.NewDefaultingCodecForScheme(
		Scheme,
		yamlSerializer,
		yamlSerializer,
		schema.GroupVersion{Version: Version},
		runtime.InternalGroupVersioner,
	)
}
```

go init 方法是引入文件，就会自动执行。

1. utilruntime.Must: 是解析错误很可能是致命错误，如果使用标准错误处理方式，则需要编写大量的错误检查和处理代码。

```go
// Must panics on non-nil errors. Useful to handling programmer level errors.
func Must(err error) {
	if err != nil {
		panic(err)   // 注意这个是painc 
	}
}
```

2. api.AddToScheme  这个是 把 secheme 添加到 SchemeBuilder 函数切片中，使用`SchemeBuilder`的目的是向`Scheme`对象注册自定义类型和编解码器，以扩展`Scheme`对象

tools/clientcmd/api/register.go

```go
var (
  // 实例一个 SchemeBuilder 函数切片
	SchemeBuilder = runtime.NewSchemeBuilder(addKnownTypes)
  // 把 SchemeBuilder 中的 AddToScheme 的方法赋值给 AddToScheme 的变量
	AddToScheme   = SchemeBuilder.AddToScheme
)
```

实现 AddToScheme 的方法代码在 k8s.io/apimachinery 的包

k8s.io/apimachinery /pkg/runtime/schema/scheme_build.go

```go
type SchemeBuilder []func(*Scheme) error   // 定义函数切片
// AddToScheme applies all the stored functions to the scheme. A non-nil error
// indicates that one function failed and the attempt was abandoned.
func (sb *SchemeBuilder) AddToScheme(s *Scheme) error {
	for _, f := range *sb {
		if err := f(s); err != nil {
			return err
		}
	}
	return nil
}

```

3. api.AddToScheme(Scheme) 与 v1.AddToScheme(Scheme) 两个都是调的都是 SchemeBuilder 函数切片，因为这个针对的两个版本：

   `tools/clientcmd/api` 子目录包含 Kubernetes 集群配置的旧版本 API。这些版本已经过时，不建议在新代码中使用。

   `tools/clientcmd/api/v1` 子目录包含 Kubernetes 集群配置的当前版本 API，建议在新代码中使用。在这个版本中，`Config` 对象包含了连接 Kubernetes API 所需的所有信息，如集群信息、认证信息和上下文信息等。



### 引用的文件在  tools/clientcmd/loadst.go

```go
// Load takes a byte slice and deserializes the contents into Config object.
// Encapsulates deserialization without assuming the source is a file.
func Load(data []byte) (*clientcmdapi.Config, error) {
	config := clientcmdapi.NewConfig()
	// if there's no data in a file, return the default object instead of failing (DecodeInto reject empty input)
	if len(data) == 0 {
		return config, nil
	}
	decoded, _, err := clientcmdlatest.Codec.Decode(data, &schema.GroupVersionKind{Version: clientcmdlatest.Version, Kind: "Config"}, config)
	if err != nil {
		return nil, err
	}
	return decoded.(*clientcmdapi.Config), nil
}

```

代码中的  clientcmdlatest.Codec.Decode 就是 解析yaml 文件为 Config 结构体,定义Config结构体的代码如下:

文件代码路径在: tools/clientcmd/types.go

```go
type Config struct {
	// Legacy field from pkg/api/types.go TypeMeta.
	// TODO(jlowdermilk): remove this after eliminating downstream dependencies.
	// +k8s:conversion-gen=false
	// +optional
	Kind string `json:"kind,omitempty"`
	// Legacy field from pkg/api/types.go TypeMeta.
	// TODO(jlowdermilk): remove this after eliminating downstream dependencies.
	// +k8s:conversion-gen=false
	// +optional
	APIVersion string `json:"apiVersion,omitempty"`
	// Preferences holds general information to be use for cli interactions
	Preferences Preferences `json:"preferences"`
	// Clusters is a map of referencable names to cluster configs
	Clusters map[string]*Cluster `json:"clusters"`
	// AuthInfos is a map of referencable names to user configs
	AuthInfos map[string]*AuthInfo `json:"users"`
	// Contexts is a map of referencable names to context configs
	Contexts map[string]*Context `json:"contexts"`
	// CurrentContext is the name of the context that you would like to use by default
	CurrentContext string `json:"current-context"`
	// Extensions holds additional information. This is useful for extenders so that reads and writes don't clobber unknown fields
	// +optional
	Extensions map[string]runtime.Object `json:"extensions,omitempty"`
}
```

下面代码就是获取 yaml 代码

```go

func main() {
	pwd, _ := os.Getwd()
	yamlPath := fmt.Sprintf("%s/test/client-test/yaml-demo/demo/%s", pwd, "nginx.yaml")
	yamlFile, err := os.ReadFile(yamlPath)
	if err != nil {
		panic(err)
	}
	// 创建解码器
	decode := yaml.NewDecodingSerializer(unstructured.UnstructuredJSONScheme)
	obj := &unstructured.Unstructured{}
	_, _, err = decode.Decode(yamlFile, nil, obj)
	if err != nil {
		panic(err)
	}
	// 将 Unstructured 对象转换为 Runtime 对象
	objMap, err := runtime.DefaultUnstructuredConverter.ToUnstructured(obj)
	if err != nil {
		panic(err)
	}
	obj.SetUnstructuredContent(objMap)

	// 使用 Runtime 对象进行操作
	fmt.Println(obj)
}

```



