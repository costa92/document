# 启动 http servce 目录
```
php swoftcli.phar run -c http:start -b bin/swoft
```

```
swoftcli run -c http:start -b bin/swoft
```

参考连接：https://www.swoft.org/documents/v2/dev-tools/swoft-cli/



进程没有成功停止导致端口占用需要kill

```
ps -ef|grep swoft|grep -v grep|awk '{print $2}'|xargs kill -9

lsof -i:18014|grep -v PID|awk '{print $2}'|xargs kill -9

lsof -i:18015|grep -v PID|awk '{print $2}'|xargs kill -9
```

