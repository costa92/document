# 常用 linux 的命令

### mkdir  创建目录的命令：
```bash
mkdir test
mkdir -p test/test  # 创建多级目录命令
```

### 创建 ssh 登录
```ruby
 ssh-keygen -t rsa
 ssh-keygen -t rsa -C "youremail@example.com" # 添加邮件
```

### df 命令 查看 linux 磁盘信息
```bash
ll -ah  # 查看目录下的文件大小情况
lsof test.log #查看谁在写入  

df -hl 查看磁盘剩余空间
df -h 查看每个根路径的分区大小
du -sh [目录名] 返回该目录的大小
du -sm [文件夹] 返回该文件夹总M数
du -h [目录名] 查看指定文件夹下的所有文件大小（包含子文件夹）
```

### 解压命令：
```bash
tar –xvf file.tar //解压 tar包
tar -xzvf file.tar.gz [//解压tar.gz](https://xn--tar-vo3e979v.gz/)
tar -xjvf file.tar.bz2 //解压 tar.bz2
tar –xZvf file.tar.Z [//解压tar.Z](https://xn--tar-vo3e979v.z/)
unrar e file.rar //解压rar
unzip file.zip //解压zip
```
总结
  1、_.tar 用 tar –xvf 解压
  2、_.gz 用 gzip -d或者gunzip 解压
  3、_.tar.gz 和 _.tgz 用 tar –xzf 解压
  4、_.bz2 用 bzip2 -d或者用bunzip2 解压
  5、_.tar.bz2用tar –xjf 解压
  6、_.Z 用 uncompress 解压
  7、_.tar.Z 用tar –xZf 解压
  8、_.rar 用 unrar e解压
  9、_.zip 用 unzip 解压


## 过滤出所有的开机启动的项目：

```shell
systemctl list-unit-files | grep enabled
systemctl enable apache.service 开机自启apache服务
systemctl disable apache.service 关闭开机自启
systemctl list-unit-files|grep iptables 查看iptables是否开机启动的状态
systemctl list-unit-files|grep enabled|cat > ~/enabled_services.txt
```

```shell
[root@localhost ~]# chkconfig --list     显示开机可以自动启动的服务
[root@localhost ~]# chkconfig --add *** 添加开机自动启动***服务
[root@localhost ~]# chkconfig --del ***   删除开机自动启动***服务
```

##  netstat -tunlp | grep 端口号，用于查看指定端口号的进程情况

几个参数含义
*   -t (tcp) 仅显示tcp相关选项
*   -u (udp)仅显示udp相关选项
*   -n 拒绝显示别名，能显示数字的全部转化为数字
*   -l 仅列出在Listen(监听)的服务状态
*   -p 显示建立相关链接的程序名


##  lsof -i:端口号 用于查看某一端口的占用情况
各列代表的含义：

*   COMMAND：进程的名称
*   PID：进程标识符
*   USER：进程所有者
*   FD：文件描述符，应用程序通过文件描述符识别该文件。如cwd、txt等
*   TYPE：文件类型，如DIR、REG等
*   DEVICE：指定磁盘的名称
*   SIZE：文件的大小
*   NODE：索引节点（文件在磁盘上的标识）
*   NAME：打开文件的确切名称

##  ss  命令
```shell
ss -antulp | grep :6443
```

## sed 命令
```shell
# 删除 22 到 90 行
sed -i '22,90d' tb_company.sql  
```


## 同步系统时间

```bash
~]# ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
~]# yum install dnf -y
~]# dnf makecache
~]# dnf install ntpdate -y
~]# ntpdate ntp.aliyun.com
```

## 查看系统错误日志
```bash
journalctl | grep erro 
journalctl --since 12:00:00 -u kubelet
```

## 修改网卡

```bash
cd /etc/sysconfig/network-scripts
vim ifcfg-eth0

TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=no
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth0
UUID=8ecfc42c-0b52-4916-bd8d-0caa56b6ff8e
DEVICE=eth0
ONBOOT=yes
IPADDR=192.168.11.102
NETMASK=255.255.255.0
GATEWAY=192.168.11.254
DNS1=202.96.134.133
DNS2=114.114.114.114

BONDING_OPTS="mode=1 miimon=50" //设置网卡的运行模式，也可以在配置文件中设置
```


## 查看网络
```bash
ifconfig
# 或 
ip add show|grep eth0
```

## # 执行sudo命令时command not found的解决办法

 方法1： 在/etc/sudoers文件内增加这么一行:Defaults secure_path=”/bin:/usr/bin:/usr/local/bin:…”, 把要用的命令path包括进去。
```bash
/etc/sudoers
```

## 查看route
1. 查看
```bash
route -n
 ip route
```
  2. 添加
```bash
route add -net 9.123.0.0 netmask 255.255.0.0 gw 9.123.0.1
```

 3. 删除
```bash
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.11.254  0.0.0.0         UG    100    0        0 eth0
10.100.122.64   192.168.11.132  255.255.255.192 UG    0      0        0 eth0
10.100.195.192  192.168.11.133  255.255.255.192 UG    0      0        0 eth0
10.100.235.192  192.168.11.102  255.255.255.192 UG    0      0        0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
172.18.122.64   192.168.11.132  255.255.255.192 UG    0      0        0 tunl0
172.28.82.192   192.168.11.102  255.255.255.192 UG    0      0        0 tunl0
172.31.91.128   192.168.11.133  255.255.255.192 UG    0      0        0 tunl0
172.31.156.64   0.0.0.0         255.255.255.192 U     0      0        0 *
192.168.11.0    0.0.0.0         255.255.255.0   U     100    0        0 eth0
```

```bash
sudo route del -net 10.100.122.64 netmask  255.255.255.192 gw 192.168.11.132
```
