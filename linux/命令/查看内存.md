# 查看内存命令

### 查看内存所有的消息
```sh
sudo dmidecode -t memory
```

### 查看内存卡槽数量

```sh
 sudo dmidecode -t memory |grep "Number Of Devices" |awk '{print $NF}'
```
或
```sh
sudo dmidecode -t memory |grep "Associated Memory Slots" |awk '{print $NF}'
```

### 查看内存数量
```sh
sudo dmidecode -t memory |grep -A16 "Memory Device$" |grep 'Size:.*MB' |wc -l
```

### 内存支持类型

```sh
sudo dmidecode -t memory |grep -A16 "Memory Device$" |grep "Type:"
```

### 每个内存频率

```sh
sudo dmidecode -t memory |grep -A16 "Memory Device$" |grep "Speed:"
```

### 每个内存大小

```sh
sudo dmidecode -t memory |grep -A16 "Memory Device$" |grep "Size:"
```