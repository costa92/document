# Ubuntu 22.04  安装 gdb-14.1 

安装 gdb14.1 需要   GMP 4.2+  与 MPFR 3.1.0+ 的扩展版本

## 安装准备

1. 下载资源

   gdb-14.1:     https://toolchains.bootlin.com/downloads/releases/sources/gdb-14.1/   也可以到官网下载其他版本地址: https://ftp.gnu.org/gnu/gdb/

   GMP-6.2.1   https://gcc.gnu.org/pub/gcc/infrastructure/gmp-6.2.1.tar.bz2

   MPFR-4.1.0：  https://gcc.gnu.org/pub/gcc/infrastructure/mpfr-4.1.0.tar.bz2		

   

2.  准备安装位置

   安装的位置为  /usr/local 路径下

   ```sh
   sudo mkdir -p /usr/local/mpfr/4.1.0
   sudo mkdir -p /usr/local/gmp/6.2.1
   sudo mkdir /usr/local/gdb
   ```

   



##  开始安装

1.  GMP

   ```sh
   tar -xvjf gmp-6.2.1.tar.bz2 
   cd gmp-6.2.1/
   
   ./configure --prefix=/usr/local/gmp/6.2.1
   make 
   sudo make install
   ```

2.   MPFR

   ```sh
   tar -xvjf mpfr-4.1.0.tar.bz2
   cd mpfr-4.1.0/
   ./configure --prefix=/usr/local/mpfr/4.1.0 --with-gmp-include=/usr/local/gmp/6.2.1/include -with-gmp-lib=/usr/local/gmp/6.2.1/lib
   make 
   sudo make install
   ```

   

3. 安装 gdb

   ```sh
   tar -xvf gdb-14.1.tar.xz
   cd gdb-14.1/
   
   ./configure --prefix=/usr/local/gdb --with-gmp=/usr/local/gmp/6.2.1 --with-mpfr=/usr/local/mpfr/4.1.0
   
   make 
   sudo make install
   ```

4. 添加环境变量

   1. ~/.bashrc 与 ~/.zshrc

      ```sh
      export GDBPATH=/usr/local/gdb
      export PATH=$PATH:$GDBPATH/bin 
      
      source ~/.bashrc  
      source ~/.zshrc
      ```

      2.fish 

      ```sh
      set -x GDBPATH /usr/local/gdb
      set -x PATH $GDBPATH/bin $PATH
      ```

      

   