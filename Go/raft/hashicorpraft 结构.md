# hashicorp/raft  结构

Raft 一致性算法论文的地址：[论文](https://github.com/maemual/raft-zh_cn)

[hashicorp/raft](https://github.com/hashicorp/raft)  是常用的 Golang Raft 算法的实现 被众多流行软件使用， 如：Consul ,  InfluxDB . IPFS等

1. Hashicorp Raft是个package，可以将它理解成库（lib），是没有main函数的。
2. raft.go 是 Hashicorp Raft 的核心代码文件，大部分的核心功能都是在这个文件中实现。
3. 提供了一个fuzzy 包，用来模拟测试 启动一个集群， 启动多个raftNode 节点。
4. 在 Hashicorp Raft 中，支持两种节点间通讯机制，内存型和 TCP 协议型，其中，内存型通讯机制，主要用于测试，2 种通讯机制的代码实现，分别在文件 inmem_transport.go 和 tcp_transport.go 中。

```sh
raft
     fuzzy
     raft.go     
     state.go  // 节点状态相关的数据结构和函数
        type RaftState uint32
        type raftState struct
     commands.go
     
```



参考实现  [raft-demo](https://github.com/vision9527/raft-demo)

