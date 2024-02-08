# scheme 作用和应用场景



定义 scheme 文件路径：staging/src/k8s.io/apimachinery/pkg/runtime/scheme.go

```go
// registration is complete.
type Scheme struct {
	// versionMap allows one to figure out the go type of an object with
	// the given version and name.
	gvkToType map[schema.GroupVersionKind]reflect.Type

	// typeToGroupVersion allows one to find metadata for a given go object.
	// The reflect.Type we index by should *not* be a pointer.
	typeToGVK map[reflect.Type][]schema.GroupVersionKind

	// unversionedTypes are transformed without conversion in ConvertToVersion.
	unversionedTypes map[reflect.Type]schema.GroupVersionKind

	// unversionedKinds are the names of kinds that can be created in the context of any group
	// or version
	// TODO: resolve the status of unversioned types.
	unversionedKinds map[string]reflect.Type

	// Map from version and resource to the corresponding func to convert
	// resource field labels in that version to internal version.
	fieldLabelConversionFuncs map[schema.GroupVersionKind]FieldLabelConversionFunc

	// defaulterFuncs is an array of interfaces to be called with an object to provide defaulting
	// the provided object must be a pointer.
	defaulterFuncs map[reflect.Type]func(interface{})

	// converter stores all registered conversion functions. It also has
	// default converting behavior.
	converter *conversion.Converter

	// versionPriority is a map of groups to ordered lists of versions for those groups indicating the
	// default priorities of these versions as registered in the scheme
	versionPriority map[string][]string

	// observedVersions keeps track of the order we've seen versions during type registration
	observedVersions []schema.GroupVersion

	// schemeName is the name of this scheme.  If you don't specify a name, the stack of the NewScheme caller will be used.
	// This is useful for error reporting to indicate the origin of the scheme.
	schemeName string
}
```



Scheme 是一个结构体，内含处理内部 Version 直接转换 ， GVK 和 Go Type 之间转换

### 什么是 GVK

在 Kubernetes 中，GVK 是指 Group、Version 和 Kind 三个字段，用于唯一标识 Kubernetes 资源对象。

	Group 指的是 Kubernetes API 中的资源组，例如 apps、batch、core 等。
	Version 指的是资源对象的 API 版本，例如 v1、v1beta1、v2alpha1 等。
	Kind 指的是资源对象的类型，例如 Pod、Service、Deployment 等。

在 Kubernetes 中，所有的资源对象都必须要有一个 GVK，以便于 Kubernetes 控制器进行操作和管理。对于一个特定的资源对象，可以通过 kubectl api-resources 命令来查看它的 GVK 信息。例如，Pod 资源对象的 GVK 是 core/v1/Pod，其中 core 是资源组，v1 是 API 版本，Pod 是资源对象类型。

查看 GVK 信息 

```sh
kubectl explain pod  // 查看 pod
```



Scheme 的填充过程

![image-20240107221944748](http://img.longqiuhong.com/picgo/img/image-20240107221944748.png)

实现 AddKnownTypes 方法文件： pkg/apis/apps/register.go

```go
// Adds the list of known types to the given scheme.
func addKnownTypes(scheme *runtime.Scheme) error {
	// TODO this will get cleaned up with the scheme types are fixed
	scheme.AddKnownTypes(SchemeGroupVersion,
		&DaemonSet{},
		&DaemonSetList{},
		&Deployment{},
		&DeploymentList{},
		&DeploymentRollback{},
		&autoscaling.Scale{},
		&StatefulSet{},
		&StatefulSetList{},
		&ControllerRevision{},
		&ControllerRevisionList{},
		&ReplicaSet{},
		&ReplicaSetList{},
	)
	return nil
}
```

​	``SchemeBuilder = runtime.NewSchemeBuilder(addKnownTypes) ``  实例化 SchemeBuilder

​	`AddToScheme = SchemeBuilder.AddToScheme` 定义了 AddToScheme 的别名，方便调用

```go
var (
	// SchemeBuilder stores functions to add things to a scheme.
	SchemeBuilder = runtime.NewSchemeBuilder(addKnownTypes)
	// AddToScheme applies all stored functions t oa scheme.
	AddToScheme = SchemeBuilder.AddToScheme
)
```



实行SchemeBuilder.AddToScheme 的方法，在 staging/src/k8s.io/apimachinery/pkg/runtime/scheme_builder.go 文件中

```go
package runtime

// SchemeBuilder collects functions that add things to a scheme. It's to allow
// code to compile without explicitly referencing generated types. You should
// declare one in each package that will have generated deep copy or conversion
// functions.
type SchemeBuilder []func(*Scheme) error

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

// Register adds a scheme setup function to the list.
func (sb *SchemeBuilder) Register(funcs ...func(*Scheme) error) {
	for _, f := range funcs {
		*sb = append(*sb, f)
	}
}

// NewSchemeBuilder calls Register for you.
func NewSchemeBuilder(funcs ...func(*Scheme) error) SchemeBuilder {
	var sb SchemeBuilder
	sb.Register(funcs...)
	return sb
}

```

如何触发 AddToScheme 

```sh

```



## Internal Verison 与 extend Version

### Internal Version

实现的代码文件 pkg/apis/apiserverinternal下

![image-20240107222851985](http://img.longqiuhong.com/picgo/img/image-20240107222851985.png)

install 文件, 以 apps 为例： 文件路径 pkg/apis/apps/install/install.go

```go
package install

import (
	"k8s.io/apimachinery/pkg/runtime"
	utilruntime "k8s.io/apimachinery/pkg/util/runtime"
	"k8s.io/kubernetes/pkg/api/legacyscheme"
	"k8s.io/kubernetes/pkg/apis/apps"
	"k8s.io/kubernetes/pkg/apis/apps/v1"
	"k8s.io/kubernetes/pkg/apis/apps/v1beta1"
	"k8s.io/kubernetes/pkg/apis/apps/v1beta2"
)

func init() {
	Install(legacyscheme.Scheme)
}

// Install registers the API group and adds types to a scheme
func Install(scheme *runtime.Scheme) {
	utilruntime.Must(apps.AddToScheme(scheme))
	utilruntime.Must(v1beta1.AddToScheme(scheme))
	utilruntime.Must(v1beta2.AddToScheme(scheme))
	utilruntime.Must(v1.AddToScheme(scheme))
	utilruntime.Must(scheme.SetVersionPriority(v1.SchemeGroupVersion,   v1beta2.SchemeGroupVersion, v1beta1.SchemeGroupVersion))
}

```

代码中有 init 方法 ，方法调用了 Install  

调用 install 文件 在 pkg/controlplane/import_known_versions.go 

```go
package controlplane

import (
	// These imports are the API groups the API server will support.
	_ "k8s.io/kubernetes/pkg/apis/admission/install"
	_ "k8s.io/kubernetes/pkg/apis/admissionregistration/install"
	_ "k8s.io/kubernetes/pkg/apis/apiserverinternal/install"
	_ "k8s.io/kubernetes/pkg/apis/apps/install"
	_ "k8s.io/kubernetes/pkg/apis/authentication/install"
	_ "k8s.io/kubernetes/pkg/apis/authorization/install"
	_ "k8s.io/kubernetes/pkg/apis/autoscaling/install"
	_ "k8s.io/kubernetes/pkg/apis/batch/install"
	_ "k8s.io/kubernetes/pkg/apis/certificates/install"
	_ "k8s.io/kubernetes/pkg/apis/coordination/install"
	_ "k8s.io/kubernetes/pkg/apis/core/install"
	_ "k8s.io/kubernetes/pkg/apis/discovery/install"
	_ "k8s.io/kubernetes/pkg/apis/events/install"
	_ "k8s.io/kubernetes/pkg/apis/extensions/install"
	_ "k8s.io/kubernetes/pkg/apis/flowcontrol/install"
	_ "k8s.io/kubernetes/pkg/apis/imagepolicy/install"
	_ "k8s.io/kubernetes/pkg/apis/networking/install"
	_ "k8s.io/kubernetes/pkg/apis/node/install"
	_ "k8s.io/kubernetes/pkg/apis/policy/install"
	_ "k8s.io/kubernetes/pkg/apis/rbac/install"
	_ "k8s.io/kubernetes/pkg/apis/scheduling/install"
	_ "k8s.io/kubernetes/pkg/apis/storage/install"
)
```

在这里调用了各种 注册资源

### extend Version

![image-20240107223259683](http://img.longqiuhong.com/picgo/img/image-20240107223259683.png)





### Converter 和 Defaulter 的代码生成

zz_generated.conversion.go 与 zz_generated.defaults.go

doc.go 文件

```go
// +k8s:conversion-gen=k8s.io/kubernetes/pkg/apis/apps
// +k8s:conversion-gen-external-types=k8s.io/api/apps/v1
// +k8s:defaulter-gen=TypeMeta
// +k8s:defaulter-gen-input=k8s.io/api/apps/v1

package v1 // import "k8s.io/kubernetes/pkg/apis/apps/v1"

```

生成 zz_generated.conversion.go 文件

```go
// +k8s:conversion-gen=k8s.io/kubernetes/pkg/apis/apps
// +k8s:conversion-gen-external-types=k8s.io/api/apps/v1
```

生成 zz_generated.defaults.go

```go
// +k8s:defaulter-gen=TypeMeta
// +k8s:defaulter-gen-input=k8s.io/api/apps/v1
```



为什么需要代码生成?