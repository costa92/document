# Ubuntu 误修改 sudoer 导致无法使用 sudo 的解决办法

经常使用 sudo 命令，但在 Ubuntu 用户登录，使用 sudo 时，要求需要输入密码，为了方便，可能会修改 root 用户下的 /etc/sudoers 文件，使 sudo 命令不需要输入密码，但是在修改是难免会出现输入错误，导致 sudo 命令无法使用，出现错误

```sh
>>> /etc/sudoers: 语法错误 near line 23 <<<
sudo: /etc/sudoers 中第 23 行附近有解析错误
sudo: 没有找到有效的 sudoers 资源，退出
sudo: 无法初始化策略插件
```

在网上查看很多资料，要么是需要到 root 用户下来修改 /etc/sudoers 文件，但是一般在使用 Ubuntu 时，都不会去设置 root 密码，这样这个方法是无法实现的。要么是重启系统，进入 grub 界面，在进入 recovery 模式，修改 /etc/sudoers 文件，在重启系统，但是主机一般都受开发人员控制，只能使用 ssh 登录使用权限。


那么可以使用 pkexec visudo 方式来处理。 但是来处理的时候，发现还是需要输入密码，输入登录密码后还是错误：

```sh
$ pkexec visudo
要以超级用户的身份运行“/usr/sbin/visudo” 程序，您必须通过验证
Authenticating as: Ubuntu,,, (Ubuntu)
Password:
polkit-agent-helper-1: pam_authenticate failed: Authentication failure
==== AUTHENTICATION FAILED ===
Error executing command as another user: Not authorized
```

**最终的解决方法：**

1、打开两个ssh登录Ubuntu系统的用户，注意一定是要两个相同的用户

2、在第一个ssh登录终端输入一下命令，来获取pid

```sh
echo $$
```

3、在另外一个ssh登录终端（第二个终端），输入一下命令：

```sh
pkttyagent --process {pid}
```
ps: {pid} 是第一个终端通过 （echo $$）的结果

4、第二终端会卡住，不需要管，选择到第一个终端输入:

```sh
 pkexec visudo
```
5、第一个终端也会卡住，需要回到第二终端输入密码，输入密码后当前终端也会卡住

6、回到第一个终端会发现出现 /etc/sudoers 文件夹的编辑界面，把出现错误的地方修改正确，保存退出

7、修改完成后，就可以继续使用sudo 命令。




