#  go 参数三剑客  pflang . viper .Cobra 



##  Pflag 特点：

pflag 包与 flag 包的工作原理甚至是代码实现都是类似的，下面是 pflag 相对 flag 的一些优势：

- 支持更加精细的参数类型：例如，flag 只支持 uint 和 uint64，而 pflag 额外支持 uint8、uint16、int32 等类型。
- 支持更多参数类型：ip、ip mask、ip net、count、以及所有类型的 slice 类型。
- 兼容标准 flag 库的 Flag 和 FlagSet：pflag 更像是对 flag 的扩展。
- 原生支持更丰富的功能：支持 shorthand、deprecated、hidden 等高级功能。

[代码传送](github.com/spf13/pflag)

# viper 特点：

- 支持 JSON/TOML/YAML/HCL/envfile/Java properties 等多种格式的配置文件；
- 可以设置监听配置文件的修改，修改时自动加载新的配置；
- 从环境变量、命令行选项和`io.Reader`中读取配置；
- 从远程配置系统中读取和监听修改，如 etcd/Consul；
- 代码逻辑中显示设置键值。

[代码传送](github.com/spf13/vipe)

# Cobra特点：

命令(Commands)，参数(Args)和标识(Flags)是Cobra重要的三个概念。

- 命令(Commands)代表动作
- 参数(Args)代表事件
- 标示(Flags)是对动作的修饰
  好的命令行应该像自然语句一样流畅，让用户一眼就明白其作用。
  例如以下例子，`docker`是根命令，`pull`是命令。

[代码传送](github.com/spf13/cobra/cobra)