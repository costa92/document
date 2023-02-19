#  k8s resource (2)

在k8s 中 TypeMeta 和 ObjectMeta 都是 Kubernetes API 对象的元数据信息，它们有不同的作用和用途。

1. **TypeMeta**：TypeMeta 描述了 Kubernetes 对象的 API 版本和种类。它包含两个字段：apiVersion 和 kind。其中，apiVersion 表示所使用的 Kubernetes API 版本，而 kind 表示对象的种类，如 Pod、Service、Deployment 等。TypeMeta 通常出现在 Kubernetes 对象定义的顶部，用于标识该对象的类型，并告诉 Kubernetes API 如何解析该对象。
2. **ObjectMeta**：ObjectMeta 是 Kubernetes 对象的一部分，用于描述对象的元数据信息。它包含多个字段，如 name、namespace、labels、annotations 等，用于标识、选择和管理 Kubernetes 对象。ObjectMeta 可以由 Kubernetes 控制器设置，也可以手动指定。它是 Kubernetes 对象的必需部分，用于描述对象的基本信息和配置。

总的来说，TypeMeta 用于描述 Kubernetes 对象的 API 版本和种类，它是 Kubernetes API 对象的必需部分，通常出现在对象定义的顶部。而 ObjectMeta 则用于描述 Kubernetes 对象的元数据信息，包括对象的名称、命名空间、标签、注释等，它是 Kubernetes 对象的一部分，用于标识、选择和管理对象。TypeMeta 和 ObjectMeta 都是 Kubernetes API 对象的重要组成部分，用于描述对象的基本信息和配置。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21.1
        ports:
        - containerPort: 80

```

在上述示例中，TypeMeta 出现在文件的第一行，用于标识该 YAML 文件所描述的 Kubernetes API 对象的类型。而 ObjectMeta 出现在文件的第 4-7 行，用于描述 Deployment 对象的元数据信息，包括名称和标签等。

源代码：staging/src/k8s.io/apimachinery/pkg/apis/meta/v1/types.go

- TypeMeta 的定义

```go
// +k8s:deepcopy-gen=false
type TypeMeta struct {
	// Kind is a string value representing the REST resource this object represents.
	// Servers may infer this from the endpoint the client submits requests to.
	// Cannot be updated.
	// In CamelCase.
	// More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds
	// +optional
	Kind string `json:"kind,omitempty" protobuf:"bytes,1,opt,name=kind"`

	// APIVersion defines the versioned schema of this representation of an object.
	// Servers should convert recognized schemas to the latest internal value, and
	// may reject unrecognized values.
	// More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources
	// +optional
	APIVersion string `json:"apiVersion,omitempty" protobuf:"bytes,2,opt,name=apiVersion"`
}
```

 Kind 定义了资源的类型，字段 APIVersion 定义了资源的 group  和 version。

- ObjectMeta 的定义:

```go

type ObjectMeta struct {
  Name string `json:"name,omitempty" protobuf:"bytes,1,opt,name=name"`

  GenerateName string `json:"generateName,omitempty" protobuf:"bytes,2,opt,name=generateName"`

  Namespace string `json:"namespace,omitempty" protobuf:"bytes,3,opt,name=namespace"`

  SelfLink string `json:"selfLink,omitempty" protobuf:"bytes,4,opt,name=selfLink"`

  UID types.UID `json:"uid,omitempty" protobuf:"bytes,5,opt,name=uid,casttype=k8s.io/kubernetes/pkg/types.UID"`

  ResourceVersion string `json:"resourceVersion,omitempty" protobuf:"bytes,6,opt,name=resourceVersion"`

  Generation int64 `json:"generation,omitempty" protobuf:"varint,7,opt,name=generation"`

  CreationTimestamp Time `json:"creationTimestamp,omitempty" protobuf:"bytes,8,opt,name=creationTimestamp"`

  DeletionTimestamp *Time `json:"deletionTimestamp,omitempty" protobuf:"bytes,9,opt,name=deletionTimestamp"`

  DeletionGracePeriodSeconds *int64 `json:"deletionGracePeriodSeconds,omitempty" protobuf:"varint,10,opt,name=deletionGracePeriodSeconds"`

  Labels map[string]string `json:"labels,omitempty" protobuf:"bytes,11,rep,name=labels"`

  Annotations map[string]string `json:"annotations,omitempty" protobuf:"bytes,12,rep,name=annotations"`

  OwnerReferences []OwnerReference `json:"ownerReferences,omitempty" patchStrategy:"merge" patchMergeKey:"uid" protobuf:"bytes,13,rep,name=ownerReferences"`

  Finalizers []string `json:"finalizers,omitempty" patchStrategy:"merge" protobuf:"bytes,14,rep,name=finalizers"`

  ClusterName string `json:"clusterName,omitempty" protobuf:"bytes,15,opt,name=clusterName"`

  ManagedFields []ManagedFieldsEntry `json:"managedFields,omitempty" protobuf:"bytes,17,rep,name=managedFields"`
}
```



注意：不是所有 Kubernetes API 对象都需要同时包含 TypeMeta 和 ObjectMeta。有些 Kubernetes API 对象（如 Namespace）只需要 ObjectMeta，而另一些 Kubernetes API 对象（如 Node）只需要 TypeMeta。在创建 YAML 文件时，需要根据 Kubernetes API 对象的类型和要设置的属性来确定应该包含哪些元数据信息。

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: <insert-namespace-name-here>

```



一般对于 resource 典型的 YAML 文件都会分为三个部分，type meta, object meta 还有 spec。

- type meta 里一般定义了 resource 的 group version 还有 kind 信息，和 API 访问路径里定义的 ${group-name} ${version} ${resource-kind} 等 path 变量直接对应。
- object meta 里一般定义 resource 的名字，所属的 namespace，以及 label 等元数据信息，会和 API 访问路径里的 ${namespace-name} 和 ${resource-name} 等 path 变量来直接对应。
- spec 里一般就是定义这个 resource 具体的属性和特性了（不同 resource spec 一定会有所不一样），会以 request body 的形式和 API 来对应。



注意： 在 Kubernetes 中的 yaml 格式有：

- Lists（列表）
- Maps（字典）

Maps :

```ymal
apiVersion: v1
kind: Pod
```

Lists:  spec.containers[]

```yaml
args
  - Cat
  - Dog
  - Fish
```

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: ydzs-site
  labels:
    app: web
spec:
  containers:
    - name: front-end
      image: nginx
      ports:
        - containerPort: 80
    - name: flaskapp-demo
      image: cnych/flaskapp
      ports:
        - containerPort: 5000
```

