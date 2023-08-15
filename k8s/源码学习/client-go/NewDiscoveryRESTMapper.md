# NewDiscoveryRESTMapper 使用说明

NewDiscoveryRESTMapper 是 Kubernetes 中的一种 REST 映射器（REST Mapper），它主要用于将 REST 请求映射到 Kubernetes API 中的对象，并根据对象的类型和 API 组进行路由。该 REST 映射器最初是在 Kubernetes v1.20 中引入的，并取代了之前的默认 REST 映射器。该 REST 映射器的主要目的是提高 Kubernetes API Server 的性能和可扩展性。

在 k8s.io/apimachinery/pkg/api/meta/interfaces.go 定义了 RESTMapper 接口

```go
//
// TODO: split into sub-interfaces
type RESTMapper interface {
	// KindFor takes a partial resource and returns the single match.  Returns an error if there are multiple matches
	KindFor(resource schema.GroupVersionResource) (schema.GroupVersionKind, error)

	// KindsFor takes a partial resource and returns the list of potential kinds in priority order
	KindsFor(resource schema.GroupVersionResource) ([]schema.GroupVersionKind, error)

	// ResourceFor takes a partial resource and returns the single match.  Returns an error if there are multiple matches
	ResourceFor(input schema.GroupVersionResource) (schema.GroupVersionResource, error)

	// ResourcesFor takes a partial resource and returns the list of potential resource in priority order
	ResourcesFor(input schema.GroupVersionResource) ([]schema.GroupVersionResource, error)

	// RESTMapping identifies a preferred resource mapping for the provided group kind.
	RESTMapping(gk schema.GroupKind, versions ...string) (*RESTMapping, error)
	// RESTMappings returns all resource mappings for the provided group kind if no
	// version search is provided. Otherwise identifies a preferred resource mapping for
	// the provided version(s).
	RESTMappings(gk schema.GroupKind, versions ...string) ([]*RESTMapping, error)

	ResourceSingularizer(resource string) (singular string, err error)
}

```

KindFor:  获取部分资源并返回单个匹配项。如果有多个匹配项，则返回错误判断加载，主要是判断是否存在查询的资源项

KindsFor: 获取部分资源并按优先顺序返回潜在种类列表。

ResourceFor: 获取部分资源并返回单个匹配项。如果有多个匹配项，则返回错误