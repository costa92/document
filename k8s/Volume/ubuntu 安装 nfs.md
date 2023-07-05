# ubuntu 安装 nfs

1. 安装nfs服务

   ```sh
   sudo apt install nfs-kernel-server
   ```

   

2. 编辑配置文件

```sh
sudo vim /etc/exports

# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/data/nfs-share *(rw,sync,no_subtree_check,no_root_squash) 
```


3. 创建共享目录

```sh
sudo mkdir -p /data/nfs-share
```

4. 重启nfs服务

```sh
sudo service nfs-kernel-server restart
```

5. 常用命令工具

   已经安装的nfs无需安装客户端

````sh
# 显示已经mount到本机上
sudo showmount -e localhost
# 将配置文件中的目录全部重新 Export一次，无需重启
sudo exportfs -rv
# 查看nfs的运行状态
sudo nfsstat
#查看rpc执行信息，可以用于检测rpc运行情况
sudo rpcinfo

#查看网络端口，NFS默认是使用111端口。
sudo netstat -tu -4
````



6. 客户端的命令

   ```sh
   # 安装客户端命令
   sudo apt install nfs-common
   ```

7. 显示指定的（192.168.2.167）NFS服务器上export出来的目录

   ```sh
   sudo showmount -e 192.168.2.167
   ```

8. 创建本地挂载目录

    ```sh
    sudo mkdir -p /mnt/data
    ```


9. 挂载共享目录

````sh
sudo mount -t nfs 192.168.3.167:/data/nfs-share /mnt/data
````



