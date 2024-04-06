# Ubuntu开启SSH免密登录

## 一、创建密钥

在客户机上输入以下命令创建一组公钥和私钥

```sh
ssh-keygen
```

查看 rsa 

```sh
ls -la ~/.ssh
```



- 密钥生成位置：默认会将密钥生成到当前登录用户的主目录下的.ssh文件夹中，如：/home/master/.ssh，建议使用默认位置，以便后续操作
- 私钥密码：默认无密码，如果设置了私钥密码，在进行免密登录时需要输入私钥密码
- 确认私钥密码：默认无密码

在 Ubuntu 的系统默认配置中，以上操作完成即可。然而，在一些不同版本的系统中，可能还需要配置以下 ssh 的配置，具体如下：
\- 备份 `/etc/ssh/sshd_config`
\- 编辑 `/etc/ssh/sshd_config` ，将 `PubkeyAuthentication no` 修改为 `PubkeyAuthentication yes`