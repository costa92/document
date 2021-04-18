# mac 安装 php72

下载 http://mirrors.sohu.com/php/php-7.2.33.tar.gz

解压：

```bash
tar -zcvf php-7.2.33.tar.gz  php72
```

 

编译参数：

```
./configure --prefix=/usr/local/php/ \
--with-config-file-path=/usr/local/php/etc \
--with-config-file-scan-dir=/usr/local/php/etc/conf.d \
--enable-pdo \
--with-pdo-mysql \
--with-mysqli \
--with-fpm-user=www \
--with-fpm-group=www \
--enable-short-tags \
--enable-opcache \
--enable-cgi \
--enable-fpm \
--enable-sockets \
--enable-mbstring \
--enable-mbregex \
--enable-bcmath \
--enable-session \
--enable-xml \
--with-zip \
--with-zlib-dir=/opt/homebrew/Cellar/zlib/1.2.11 \
--enable-gd \
--with-zlib \
--with-mhash \
--enable-pcntl \
--with-xmlrpc \
--enable-soap \
--enable-mysqlnd \
--enable-maintainer-zts \
--enable-ftp \
--with-curl=/opt/homebrew/opt/curl \
--enable-inline-optimization \
--enable-sysvsem \
--enable-shmop \
--with-freetype \
--with-gettext \
--with-iconv=/opt/homebrew/opt/libiconv \
--with-openss
```

编译错信息处理：

1. 错误提示为：configure: error: Please specify the install prefix of iconv with --with-iconv=<DIR>

根据提示安装了下libiconv

```
brew install libiconv

==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/bottles/libiconv-1.16.arm64
######################################################################## 100.0%
==> Pouring libiconv-1.16.arm64_big_sur.bottle.tar.gz
==> Caveats
libiconv is keg-only, which means it was not symlinked into /opt/homebrew,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have libiconv first in your PATH, run:
  echo 'export PATH="/opt/homebrew/opt/libiconv/bin:$PATH"' >> ~/.zshrc

For compilers to find libiconv you may need to set:
  export LDFLAGS="-L/opt/homebrew/opt/libiconv/lib"
  export CPPFLAGS="-I/opt/homebrew/opt/libiconv/include"

==> Summary
🍺  /opt/homebrew/Cellar/libiconv/1.16: 31 files, 2.6MB
```

注意： /opt/homebrew/opt/libiconv 是安装 brew 安装libiconv的位置

所以需要修改 --with-iconv 的值

```
--with-iconv=/opt/homebrew/opt/libiconv
```



2. configure: error: Cannot locate header file libintl.h

解决办法：

安装 gettext

```sh
brew install gettext
```

   要注意返回的安装的地址：/opt/homebrew/Cellar/gettext

在编辑 configure 文件，在文件中找到 

```
for i in $PHP_GETTEXT /usr/local /usr ; do
```

修改成：

```vim
for i in $PHP_GETTEXT /usr/local /usr /opt/homebrew/Cellar/gettext; do
```

重新编译：./configure



m1的PHP 安装的位置

​	 

```bash
/opt/homebrew/opt/php
```



mac 的 phpize

```
/opt/homebrew/Cellar/php@7.3/7.3.27/bin/phpize
```

mac 的 phpize

```bash
/opt/homebrew/Cellar/php@7.3/7.3.27/bin/php-config
```



```
./configure --with-php-config=/opt/homebrew/Cellar/php@7.3/7.3.27/bin/php-config --enable-openssl  --enable-http2 --with-openssl-dir=/opt/homebrew/Cellar/openssl@1.1/1.1.1j
```

```
brew reinstall openssl
pecl install -a --nobuild swoole \
    && cd "$(pecl config-get temp_dir)/swoole" \
    && phpize \
    && ./configure --with-openssl-dir=/opt/homebrew/Cellar/openssl@1.1/1.1.1j --enable-sockets --enable-openssl --enable-http2 --enable-mysqlnd \
    && make \
    && make install \
    && echo "extension=\"swoole.so\"" >> $(php -i | grep "php.*php.ini" | rev | cut -d' ' -f1 | rev)
```

