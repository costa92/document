### 常用的grep的命令

1. 提取文档中的未加 “#” 号的文字
```shell
 grep -v -e '#' -e ^$ cluster.yaml
```

2. 删除 swoft 的进程

```shell
ps -aux|grep swoft|awk '{
```

```shell
ifconfig eth0|grep "inet"|awk -F" " '{print $0}'|awk '{print $2}'
```



3 这两个命令结合用来批量结束进程：

```sh
ps -ef | grep nsq | grep -v grep | awk '{print $2}' | xargs kill #杀掉所有nsq相关进程
```



###  反向匹配

```sh
$ grep -v
```

###  输出上下文

需要输出与匹配行相邻的行，可以通过 *-C* 参数实现。同时输出匹配行前后 *10* 行：

```sh
$ grep -C 10
```

###  查询文件字符串数量

在 file 文件中含有 haha 数量

```sh
grep -o 'haha' file | wc -l
```





https://gregable.com/2010/09/why-you-should-know-just-little-awk.html
