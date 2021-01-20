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

