# ubuntun 安装 php7.2

1. 下载：

``` bash
 wget http://cn2.php.net/distributions/php-7.2.3.tar.gz
```

2. 解压命令

```shell
tar -xzxvf php-7.2.3.tar.gz
```

3. 安装扩展：

```bash
sudo apt install gcc make openssl curl libbz2-dev libxml2-dev libjpeg-dev libfreetype6-dev libzip-dev libssl-dev -y
```

4. 预编译

```bash
./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --enable-fpm --with-fpm-user=www --with-fpm-group=www --with-mysqli --with-pdo-mysql --with-iconv-dir --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml --disable-rpath --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl-dir=/usr/bin/curl --enable-mbregex --enable-mbstring --enable-ftp --with-gd --with-openssl --with-mhash --enable-pcntl --enable-sockets --with-xmlrpc --enable-zip --enable-soap --without-pear --with-gettext --disable-fileinfo --enable-maintainer-zts

```

5. 编译并安装 ，参数-j指定编译线程数量来进行多线程编译，不想等着就加上咯

```bash
sudo make -j4
sudo make install
```

## 错误：configure: error: freetype-config not found.

1. Ubuntu 20.04 编译php7.3.18报错 freetype-config not found
安装 libfreetype6-dev

```bash
sudo apt-get install libfreetype6-dev
```
但是安装后，重新编译安装php 还行出现同样的错误


Ubuntu20.04是比较新的版本，是不是freetype版本兼容的问题。 通过

``` bash
sudo apt-get changelog libfreetype6-dev
```
得到Ubuntu 20.04 中使用的版本是2.10.1-2.

在changelog中有一段:

``` bash
New upstream release (Closes: #901052):
Avoid dereferencing a NULL pointer (CVE-2018-6942) (Closes: #890450).
The `freetype-config’ script is no longer installed by default (Closes: #871470, #886461). All packages depending on libfreetype6-dev should use pkg-config to find the relevant CFLAGS and libraries.
```

地址是：http://changelogs.ubuntu.com/changelogs/pool/main/f/freetype/freetype_2.10.1-2/changelog


freetype-config脚本不再默认支持，而是替换成了pkg-config，用来管理CFLAGS和库。

通过这些变更就知道，我们需要把php编译脚本中的freetype-config改成pkg-config。

我们可以再configure中找到freetype-config,完成替换。还可以使用以下命令替换。

```bash
cd php-src/

sed -i "s/freetype-config/pkg-config/g" ./ext/gd/config.m4
sed -i "s/FREETYPE2_CONFIG --cflags/FREETYPE2_CONFIG freetype2 --cflags/g" ./ext/gd/config.m4
sed -i "s/FREETYPE2_CONFIG --libs/FREETYPE2_CONFIG freetype2 --libs/g" ./ext/gd/config.m4

./buildconf --force

``` 

错误：
``` bash
buildconf: checking installation...
buildconf: autoconf not found.
           You need autoconf version 2.64 or newer installed
           to build PHP from Git.
make: *** [build/build.mk:37：buildmk.stamp] 错误 1
```

