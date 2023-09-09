# 如何限制命名空间中的可用资源总量 ResourceQuota

可以使用 ResourceQuota 对象来检查将要创建的pod 是否会引起总资源量的使用超出限额，如果超出限额， 创建请求会被拒绝

ResourceQuota 代码中

```go
// pkg/apis/core/types.go
// +k8s:deepcopy-gen:interfaces=k8s.io/apimachinery/pkg/runtime.Object

// ResourceQuota sets aggregate quota restrictions enforced per namespace
type ResourceQuota struct {
	metav1.TypeMeta
	// +optional
	metav1.ObjectMeta

	// Spec defines the desired quota
	// +optional
	Spec ResourceQuotaSpec

	// Status defines the actual enforced quota and its current usage
	// +optional
	Status ResourceQuotaStatus
}
...

// ResourceQuotaSpec defines the desired hard limits to enforce for Quota
type ResourceQuotaSpec struct {
	// Hard is the set of desired hard limits for each named resource
	// +optional
	Hard ResourceList
	// A collection of filters that must match each object tracked by a quota.
	// If not specified, the quota matches all objects.
	// +optional
	Scopes []ResourceQuotaScope
	// ScopeSelector is also a collection of filters like Scopes that must match each object tracked by a quota
	// but expressed using ScopeSelectorOperator in combination with possible values.
	// +optional
	ScopeSelector *ScopeSelector
}


```

 资源配额在 pod 创建的时候进行检查，因此 ResourceQuota 对象仅仅作用于在其后创建的 pod 但并影响已经存在的 pod

创建 ResourceQuote 对象: mem-cpu-demo.yaml

```yaml
apiVersion: v1
kind: ResourceQuota
metadata: 
	name: mem-cpu-demo
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
```

创建 ResourceQuota 对象

```sh
kubectl create -f mem-cpu-demo.yaml
```

创建一个符合quota限制的pod

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: quota-mem-cpu-demo
spec:
	containers:
	- name: quota-mem-cpu-demo-ctr
		image: nginx
		resources:
		  limits:
		    memory: "800Mi"
		    cpu: "800m"
		  requests:
		    memory: "600Mi"
		    cpu: "400m"
		    
```

创建

```sh
kubectl apply -f mem-cpu-demo.yaml
```

查看当前 ResourceQuote 的使用情况

```sh
 kubectl get resourcequota mem-cpu-demo  --output=yaml
```

