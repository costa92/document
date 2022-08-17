# 解决System limit for number of file watchers reached

启动 vue 项目，使用  `yarn serve `或  `npm run dev` 启动项目，会报错如下:

Error: ENOSPC: System limit for number of file watchers reached, 

![image-20220817143308285](https://file.longqiuhong.com/uploads/picgo/image-20220817143308285.png)



## 解决办法

原因：文件监视程序的系统产生了限制，达到了默认的上限，需要增加限额。

查看限制文件个数：

```sh
cat /proc/sys/fs/inotify/max_user_watchs
```

1. 临时增加限额

```sh
sudo sysctl fs.inotify.max_user_watches=524288 
sudo sysctl -p
```

2. 永久增加限额

```sh
echo fs.inotify.max_user_watches = 524288 | sudo tee -a /etc/sysctl.conf 
sudo sysctl -p
```

