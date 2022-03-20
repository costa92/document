# big sur 安装PHP 扩展

 **查询 PHP 版本** 

```sh
php -v
 
输出：
WARNING: PHP is not recommended
PHP is included in macOS for compatibility with legacy software.
Future versions of macOS will not include PHP.
PHP 7.3.24-(to be removed in future macOS) (cli) (built: Jun 17 2021 21:41:15) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.24, Copyright (c) 1998-2018 Zend Technologies
```



输出的 WARNING 是因为 big sur 的安全机制导致的。不过可以是 brew 安装，按照的教程参考：https://blog.csdn.net/silk_java/article/details/111769920

网上很多都提供了是关闭 系统完整性保护（SIP）那么接下来一波操作如虎。

1. 重启mac
2. 按住 Command + R 不放，直到看苹果 logo 才放下，然后等待片刻进入macOS恢复模式
3. 在顶部菜单点击“实用工具”→“终端”打开终端

![image-20210728090942258](/Users/costalong/Library/Application Support/typora-user-images/image-20210728090942258.png)



4. 在终端输入：

   ```sh
   csrutil disable
   ```

5. 然后重启Mac

   

   **注意：macOS 10.15及以上的版本在关闭SIP重启系统后还需要在终端运行命令：“sudo mount -uw /”（不含引号）才能获取完全权限**



6. 使用 sudo mount -uw / 命令报错

   ```sh
   mount_apfs: volume could not be mounted: Permission denied
   
   mount: / failed with 66
   ```

   网上查了资料发现：

   Big Sur 新增了 Signed System Volume 机制，对系统所在的 APFS Volume 增加了更多的保护，需要关闭 authenticated-root 

7. 重复以前上 1，2，3，4，5 的操作但是注意 4的操作命令改成如下：

   ```sh
   csrutil authenticated-root disable
   ```

   ​	

   参考 https://www.cnblogs.com/lduml/p/13646690.html 文章有操作打开 bigsur 的安全SIP机制

   

   

   ## 重点来了

上面的操作已经全部完成后，发现安装php的扩展还是不行，那么只能另外想办法了。



在编译安装的一直报错：

```sh
➜  pcntl ~/php-private/phpize
Configuring for:
PHP Api Version:         20180731
Zend Module Api No:      20180731
Zend Extension Api No:   320180731
autom4te: error: need GNU m4 1.4 or later: /usr/local/opt/m4/bin/m4
```

导致无法生成  configaure 文件，无法编译安装下去。错误提示 /usr/local/opt/m4/bin/m4 文件不存在，那么就只能安装了。

```sh
brew install m4
```



安装后，重新执行 ~/php-private/phpize 命令，发现还是报同样的错误。以为没有安装，所以执行 

```sh
m4 --version

GNU M4 1.4.6
Copyright (C) 2006 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

Written by Rene' Seindal.
```

运行的结果明显是按照，于是想到是不是安装的文件位置不是 /usr/local/opt/m4/bin/m4 ，查询一看还是真是，那么怎么办呢？

第一种 修改/usr/local/bin/autom4te 文件

![image-20210728095408547](/Users/costalong/Library/Application Support/typora-user-images/image-20210728095408547.png)

改成 m4 安装的位置

```sh
$ brew list m4

/usr/local/Cellar/m4/1.4.18/bin/m4
/usr/local/Cellar/m4/1.4.18/share/info/ (3 files)
/usr/local/Cellar/m4/1.4.18/share/man/man1/m4.1


```

把上面 autom4te的文件 94 行：

```sh
my $m4 = $ENV{"M4"} || '/usr/local/opt/m4/bin/m4'
改成：
my $m4 = $ENV{"M4"} || '/usr/local/Cellar/m4/1.4.18/bin/m4'
```

再保存退出。却发现无法退出。因为 SIP的机制。



第二种 创建软连接：

```
ln -s /usr/local/Cellar/m4/1.4.18 /usr/local/opt/m4
```



在编译安装，就没有问题的。

```sh
➜  pcntl ~/php-private/phpize
➜  ./configure --enable-pcntl --with-php-config=/Users/xxx/php-private/php-config
➜ make && make install
➜ sudo vim /etc/php.ini  // add extension=pcntl.so



```





