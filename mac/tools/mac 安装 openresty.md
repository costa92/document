# mac 安装openresty

1. 下载
``` bash
wget https://openresty.org/download/openresty-1.19.3.1.tar.gz
tar zxvf openresty-1.19.3.1.tar.gz
```

2. 安装
``` bash
./configure \
   --with-cc-opt="-I/opt/homebrew/opt/openssl/include/ -I/opt/homebrew/opt/pcre/include/" \
   --with-ld-opt="-L/opt/homebrew/opt/openssl/lib -L/opt/homebrew/opt/pcre/lib" \
   -j8
   make 
   make install
```

3. 修改配置消息

```bash
vim ~/.zshrc
export PATH="/usr/local/openresty/bin:$PATH"
source ~/.zshrc
```
