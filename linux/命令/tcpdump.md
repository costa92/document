# tcpdump

tcpdump 支持针对网络层、协议、主机、网络或端口的过滤，并提供and、or、not等逻辑语句来帮助你去掉无用的信息


### 命令格式：

```sh
tcpdump [ -DenNqvX ] [ -c count ] [ -F file ] [ -i interface ] [ -r file ]
        [ -s snaplen ] [ -w file ] [ expression ]
```

抓包选项：
-c：指定要抓取的包数量。

-i interface：指定tcpdump需要监听的接口。默认会抓取第一个网络接口

-n：对地址以数字方式显式，否则显式为主机名，也就是说-n选项不做主机名解析。

-nn：除了-n的作用外，还把端口显示为数值，否则显示端口服务名。

-P：指定要抓取的包是流入还是流出的包。可以给定的值为"in"、"out"和"inout"，默认为"inout"。

-s len：设置tcpdump的数据包抓取长度为len，如果不设置默认将会是65535字节。对于要抓取的数据包较大时，长度设置不够可能会产生包截断，若出现包截断，
：输出行中会出现"[|proto]"的标志(proto实际会显示为协议名)。但是抓取len越长，包的处理时间越长，并且会减少tcpdump可缓存的数据包的数量，
：从而会导致数据包的丢失，所以在能抓取我们想要的包的前提下，抓取长度越小越好。

输出选项：
-e：输出的每行中都将包括数据链路层头部信息，例如源MAC和目标MAC。

-q：快速打印输出。即打印很少的协议相关信息，从而输出行都比较简短。

-X：输出包的头部数据，会以16进制和ASCII两种方式同时输出。

-XX：输出包的头部数据，会以16进制和ASCII两种方式同时输出，更详细。

-v：当分析和打印的时候，产生详细的输出。

-vv：产生比-v更详细的输出。
-vvv：产生比-vv更详细的输出。


tcpdump示例
==**tcpdump只能抓取流经本机的数据包 **==

1. 默认启动
```sh
tcpdump
```
默认情况下，直接启动tcpdump将监视第一个网络接口(非lo口)上所有流通的数据包。这样抓取的结果会非常多，滚动非常快。

2 . 监视指定网络接口的数据包
```sh
tcpdump -i ens33
```
3. 监视指定主机的数据包，例如所有进入或离开node1的数据包
```sh
tcpdump -i ens33  host node1
```
4. 打印node1<-->node2或node1<-->node3之间通信的数据包
```sh
tcpdump -i ens33 host node1 and \(node2 or node3\)
```
5. 打印node1与任何其他主机之间通信的IP数据包,但不包括与node4之间的数据包
```sh
tcpdump -i ens33 host node1 and not node4
```
6. 截获主机node1 发送的所有数据
```sh
tcpdump -i ens33 src host node1
```
7. 监视所有发送到主机node1 的数据包
```sh    
tcpdump -i ens33 dst host node1
```
8. 监视指定主机和端口的数据包
```sh
tcpdump -i ens33 port 8080 and host node1
```
9. 监视指定网络的数据包，如本机与192.168网段通信的数据包，"-c 10"表示只抓取10个包
```sh
tcpdump -i ens33 -c 10 net 192.168
```
10. 打印所有通过网关snup的ftp数据包
```sh
tcpdump 'gateway snup and (port ftp or ftp-data)'
```
注意,表达式被单引号括起来了,这可以防止shell对其中的括号进行错误解析

11. 抓取ping包
```sh
tcpdump -c 5 -nn -i ens33 
```
==指定主机抓ping包==
```sh
tcpdump -c 5 -nn -i eth0 icmp and src 192.168.100.62
```
12. 抓取到本机22端口包
```sh
tcpdump -c 10 -nn -i ens33 tcp dst port 22
```

13. 解析包数据
```sh
tcpdump -c 2 -q -XX -vvv -nn -i ens33 tcp dst port 22
```

