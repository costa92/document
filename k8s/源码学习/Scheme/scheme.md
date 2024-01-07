# scheme 作用和应用场景



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

## Internal Verison 与 extend Version

### Internal Version

实现的代码文件 pkg/apis/apiserverinternal下

![image-20240107222851985](http://img.longqiuhong.com/picgo/img/image-20240107222851985.png)

install 文件

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
	utilruntime.Must(scheme.SetVersionPriority(v1.SchemeGroupVersion, v1beta2.SchemeGroupVersion, v1beta1.SchemeGroupVersion))
}

```

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