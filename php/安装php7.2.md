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




编译错误与解决：

* configure: error: jpeglib.h not found.

```shell
 yum install libjpeg libpng freetype libjpeg-devel libpng-devel freetype-devel -y
```

* configure: error: Unable to locate gmp.h
```shell
yum install gmp-devel -y
```

* configure: error: xslt-config not found. Please reinstall the libxslt >= 1.1.0 distribution
```shell
yum -y install libxslt-devel
```

* configure: error: GNU M4 1.4 is required

```shell
yum install m4 -y
```



