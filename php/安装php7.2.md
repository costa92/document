 查看系统信息：
linux查看版本当前操作系统发行信息 cat /etc/issue 或 cat /etc/centos-release
```sh
CentOS release 6.9 (Final)
```

下载php

```shell
wget http://cn2.php.net/distributions/php-7.2.3.tar.gz
```

解压命令

```shell
tar -xzxvf php-7.2.3.tar.gz
```

安装插件

```shell
yum install -y gcc libxml2-devel openssl openssl-devel curl-devel
```


编译安装命令：
```shell
./configure  --prefix=/usr/local/php --with-config-file-path=/etc --enable-fpm --with-fpm-user=nginx --with-fpm-group=nginx --enable-inline-optimization --disable-debug --disable-rpath --enable-shared --enable-soap --with-xmlrpc --with-openssl --with-mcrypt --with-pcre-regex --with-sqlite3 --with-zlib --enable-bcmath --with-iconv --with-bz2 --enable-calendar --with-curl --with-cdb --enable-dom --enable-exif --enable-fileinfo --enable-filter --with-pcre-dir --enable-ftp --with-gd --with-openssl-dir --with-jpeg-dir --with-png-dir --with-freetype-dir --enable-gd-native-ttf --enable-gd-jis-conv --with-gettext --with-gmp --with-mhash --enable-json --enable-mbstring --enable-mbregex --enable-mbregex-backtrack --with-libmbfl --with-onig --enable-pdo --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --with-zlib-dir --with-pdo-sqlite --with-readline --enable-session --enable-shmop --enable-simplexml --enable-sockets --enable-sysvmsg --enable-sysvsem --enable-sysvshm --enable-wddx --with-libxml-dir --with-xsl --enable-zip --enable-mysqlnd-compression-support --with-pear --enable-opcache
```

编译完安装
```shell
make && make install
```

添加环境变量:

```shell
echo -e '\n export PATH=/usr/local/php/bin:/usr/local/php/sbin:$PATH\n' >> /etc/profile && source /etc/profile
```


编译错误与解决：

* configure: error: jpeglib.h not found.

```shell
 yum install libjpeg libpng freetype libjpeg-devel libpng-devel freetype-devel -y
```

* configure: error: Unable to locate gmp.h

```shell
yum install gmp-devel -y
```

* configure: error: Please reinstall the BZip2 distribution

```shell
yum install bzip2 bzip2-devel
```

* configure: error: Please reinstall readline - I cannot find readline.h

```shell
yum -y install readline-devel
```

* configure: error: xslt-config not found. Please reinstall the libxslt >= 1.1.0 distribution

```shell
yum install -y libxslt-devel*
```

* configure: error: GNU M4 1.4 is required

```shell
yum install m4 -y
```


安装扩展：

对 php 扩展进行编译安装时，出现下面的提示
```sh
Cannot find autoconf. Please check your autoconf installation and the $PHP_AUTOCONF environment variable. Then, rerun this script.
```
解决：
```sh
yum install autoconf
```





下载PHP扩展的的 [PHP扩展下载](https://pecl.php.net/package-stats.php)

* 安装 swoole

```shell
wget https://pecl.php.net/get/swoole-4.4.18.tgz
tar zxvf swoole-4.4.18.tgz
cd swoole-4.4.18
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config --enable-coroutine --enable-openssl  --enable-http2  --enable-async-redis --enable-sockets --enable-mysqlnd 
make && make install

vim /etc/php.ini

# add
extension=swoole.so
```

* 安装 redis 扩展

```shell
wget https://pecl.php.net/get/redis-4.2.0.tgz
tar zxvf redis-4.2.0.tgz && cd redis-4.2.0
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config 
make && make install

vim /etc/php.ini
# add
extension=redis.so
```
* 安装 kafka 扩展

```shell
wget https://pecl.php.net/get/rdkafka-4.0.2.tgz
tar zxvf rdkafka-4.0.2.tgz && cd rdkafka-4.0.2/
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config 
make && make install
vim /etc/php.ini
# add
extension=rdkafka.so
```

错误信息：
configure: error: Please reinstall the rdkafka distribution

```
 git clone https://github.com/edenhill/librdkafka.git
cd librdkafka
./configure
make && make install
```
* 安装 amqp 扩展

```shell
wget https://pecl.php.net/get/amqp-1.8.0.tgz
tar zxvf amqp-1.8.0.tgz && cd amqp-1.8.0 
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config --with-amqp --with-librabbitmq-dir=/usr/local/rabbitmq-c-0.9.0
make && make install

vim /etc/php.ini
# add
extension=amqp.so
```

编译错误
checking for amqp using pkg-config... configure: error: librabbitmq not found

```shell
wget https://github.com/alanxz/rabbitmq-c/archive/v0.9.0.tar.gz
tar -xzxvf v0.9.0.tar.gz && cd rabbitmq-c-0.9.0
yum -y install cmake
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/rabbitmq-c-0.9.0
make && make install
cp -R /usr/local/rabbitmq-c-0.9.0/lib64 /usr/local/rabbitmq-c-0.9.0/lib
```


注意：
发现点蛛丝马迹，上面进入了/usr/local/rabbitmq-c-0.9.0/lib 目录，查看一下发现/usr/local/rabbitmq-c-0.9.0/没有lib，但有个lib64位。

```shell
cp -R /usr/local/rabbitmq-c-0.9.0/lib64/ /usr/local/rabbitmq-c-0.9.0/lib
```

* 安装 mongodb 扩展


```shell
wget https://pecl.php.net/get/mongodb-1.7.4.tgz
tar zxvf mongodb-1.7.4.tgz && cd mongodb-1.7.4 
/usr/local/php/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config 
make && make install

vim /etc/php.ini
# add
extension=mongodb.so
```


```shell
# 安装依赖包
yum install libmcrypt libmcrypt-devel mcrypt mhash

 wget https://pecl.php.net/get/mcrypt-1.0.4.tgz
 tar zxvf mcrypt-1.0.4.tgz && cd mcrypt-1.0.4
 /usr/local/php/bin/phpize
 ./configure --with-php-config=/usr/local/php/bin/php-config

make && make install
vim /etc/php.ini
# add
extension=mcrypt.so
```

安装 composer 

```shell
php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');"
php composer-setup.php 
php -r "unlink('composer-setup.php');"

mv composer.phar /usr/local/bin/composer



# 设置全局阿里云镜像
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

#恢复到packagist官方源命令
composer config -g --unset repos.packagist


# 更新
composer selfupdate
```
