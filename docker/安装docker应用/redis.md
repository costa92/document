#  docker安装 redis

1. redis的镜像查找

```sh
docker search redis
```

2. 下载redis镜像

```sh
docker pull redis:latest
```

3. 运行镜像

```sh
docker run -itd --name redis-test -p 6379:6379 redis
```

