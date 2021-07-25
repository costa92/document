# grpc 安装

**安装**

gRPC 

```sh
go get -u google.golang.org/grpc
```

Protocol Buffers v3

```sh
wget https://codeload.github.com/protocolbuffers/protobuf/tar.gz/refs/tags/v3.17.3
tar -xzvf protobuf-3.17.3.tar.gz && cd protobuf-3.17.3
./configure
make 
make install

```

检查是否安装成功

```sh
protoc --version
```

若出现以下错误，执行 `ldconfig` 命名就能解决这问题

```sh
protoc: error while loading shared libraries: libprotobuf.so.15: cannot open shared object file: No such file or directory
```

**Protoc Plugin**

```sh
go get -u github.com/golang/protobuf/protoc-gen-go
```

