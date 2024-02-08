# GetOpenAPIDefinitions 错误

## 启动 apiservice

```bash
go run apiservice.go
```

会报错：

```sh
undefined："k8s.io/kubernetes/pkg/generated/openapi“.GetOpenAPIDefinitions
```

##  查看 openapi 代码目录

```bash
cd k8s.io/kubernetes/pkg/generated && ls
```

![image-20240106030544280](http://img.longqiuhong.com/picgo/img/image-20240106030544280.png)

查找命令:

```sh
rg GetOpenAPIDefinitions
```

在代码中搜索中，没有找到相关的 GetOpenAPIDefinitions

## 生成代码

在 build/root/Makefile 文件中可以找 以下代码

```makefile
.PHONY: generated_files
ifeq ($(PRINT_HELP),y)
generated_files:
	@echo "$$GENERATED_FILES_HELP_INFO"
else
generated_files gen_openapi:
	$(MAKE) -f Makefile.generated_files $@ CALLED_FROM_MAIN_MAKEFILE=1
endif
```

回到项目的根目录执行 `make generated_files` 命令

执行命令后，在回到 k8s.io/kubernetes/pkg/generated 目录下，可以看到新添加 zz_generated.openapi.go 文件，在文件中可以找  GetOpenAPIDefinitions 方法

```go
func GetOpenAPIDefinitions(ref common.ReferenceCallback) map[string]common.OpenAPIDefinition {
	return map[string]common.OpenAPIDefinition{
    ...
  }
}
```

再重新编译运行就不会程序错误。