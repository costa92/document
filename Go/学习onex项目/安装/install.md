# 部署 onex 项目



部署环境是：腾讯云的 ubuntu 22.04

安装过程的问题：

1. 安装 [ MariaDB](https://konglingfei.com/onex/installation/sbs.html#一键安装-使用脚本快速安装和卸载-mariadb)

```bash
 ./scripts/installation/mariadb.sh onex::mariadb::sbs::install 
```

注意： mariabd 使用的是阿里云的镜像，但是我的服务器是在腾讯云上，无法使用阿里云的镜像，需要修改镜像地址

```sh
echo ${LINUX_PASSWORD} | sudo -S echo "deb [arch=amd64,arm64] https://mirrors.aliyun.com/mariadb/repo/11.2.2/debian/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/mariadb-11.2.2.list

```

替换成:

```sh
echo ${LINUX_PASSWORD} | sudo -S echo "deb [arch=amd64,arm64] https://mirrors.cloud.tencent.com/mariadb/repo/11.2/ubuntu/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/mariadb-11.2.2.list
```

2. 安装 MongoDB的 

   执行 下面的命令之前，需要安装 jq 

   ```bash
   ./scripts/installation/mariadb.sh onex::mariadb::sbs::install 
   ```

   安装jq的 命令

   ```sh
   sudo apt install jq
   ```

​		会出现无法安装： mongodb-mongosh

​		需要在 onex::mariadb::sbs::install  函数中暂时屏蔽下面的 onex::mariadb::sbs::install  函数中的代码：onex::mongo::pre_install

​		因为：还没加入 添加 MongoDB APT 源的时候就执行了

3. 安装docker

   在 /etc/apt/sources.list.d文件下，添加：docker.list 文件内容：

   ```sh
   deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/ jammy stable
   ```

   更新 apt 包索引。

   ```sh
   sudo apt-get update
   ```

​		装最新版本的 Docker Engine-Community 和 containerd ，或者转到下一步安装特定版本：
```sh
	sudo apt-get install docker-ce docker-ce-cli containerd.io
```

系统给用户添加docker权限
```sh
sudo groupadd docker
```

将当前用户添加到 docker 组中

```sh
sudo gpasswd -a user docker
```

​	user 是当前登录的用户,可以通过 `sudo cat /etc/shadow` 查看系统用户

更新 docker 用户组

```sh
newgrp docker
```

重启 docker 服务
```sh
sudo service docker restart
```

4. 在安装 etcd 添加到系统服务中  （ /lib/systemd/system/etcd.service）

   ```sh
   # Etcd systemd unit template from
   # https://github.com/etcd-io/etcd/blob/main/contrib/systemd/etcd.service
   [Unit]
   Description=etcd key-value store # 指定了单元的描述，即 etcd 键值存储
   Documentation=https://github.com/etcd-io/etcd # 提供了指向 etcd 项目文档的链接
   After=network-online.target local-fs.target remote-fs.target time-sync.target # 指定了服务的启动顺序
   Wants=network-online.target local-fs.target remote-fs.target time-sync.target # 指定了服务的启动依赖
   
   [Service]
   Type=notify # 指定了服务的类型。notify 类型表示服务会在准备就绪时发送通知
   ExecStart=/usr/bin/etcd --config-file=/etc/etcd/config.yaml # 指定了服务启动时要执行的命令，这里是使用指定的配置文件启动 etcd
   Restart=always # 指定了服务的重启行为。always 表示服务会在退出时总是被重启
   RestartSec=10s # 指定了重启的间隔时间
   LimitNOFILE=40000 # 指定了服务的文件描述符限制，这里设置为 40000
   
   [Install]
   WantedBy=multi-user.target # 指定了服务的安装目标，这里表示服务会被添加到 multi-user.target，以便在多用户模式下启动
   ```

   需要把注释换行不能同一行：

   ```sh
   # Etcd systemd unit template from
   # https://github.com/etcd-io/etcd/blob/main/contrib/systemd/etcd.service
   [Unit]
   Description=etcd key-value store
   # 指定了单元的描述，即 etcd 键值存储
   Documentation=https://github.com/etcd-io/etcd
   # 提供了指向 etcd 项目文档的链接
   After=network-online.target local-fs.target remote-fs.target time-sync.target
   # 指定了服务的启动顺序
   Wants=network-online.target local-fs.target remote-fs.target time-sync.target
   # 指定了服务的启动依赖
   
   [Service]
   Type=notify
   # 指定了服务的类型。notify 类型表示服务会在准备就绪时发送通知
   ExecStart=/usr/bin/etcd --config-file=/etc/etcd/config.yaml
   # 指定了服务启动时要执行的命令，这里是使用指定的配置文件启动 etcd
   Restart=always
   # 指定了服务的重启行为。always 表示服务会在退出时总是被重启
   RestartSec=10s
   # 指定了重启的间隔时间
   LimitNOFILE=40000
   # 指定了服务的文件描述符限制，这里设置为 40000
   
   [Install]
   WantedBy=multi-user.target
   # 指定了服务的安装目标，这里表示服务会被添加到 multi-user.target，以便在多用户模式下启动
   ```

   5. 启动时：需要修改 scripts/gen-certs.sh 文件中 cfssl gencert 命令

      去掉： -loglevel 2  参数

   ```sh
     if [[ ! -r "ca.pem" || ! -r "ca-key.pem" ]]; then
       ${CFSSL_BIN} gencert -loglevel 2 -initca ca-csr.json | ${CFSSLJSON_BIN} -bare ca -
     fi
     ...
     #echo "Generate "${prefix}" certificates..."
     echo '{"CN":"'"${prefix}"'","hosts":[],"key":{"algo":"rsa","size":2048},"names":[{"C":"CN","ST":"Shenzhen","L":"Shenzhen","O":"tencent","OU":"'"${prefix}"'"}]}' \
       | ${CFSSL_BIN} gencert -loglevel 2 -hostname="${CERT_HOSTNAME},${prefix/-/.}.${ONEX_DOMAIN}" -ca=ca.pem -ca-key=ca-key.pem \
       -config=ca-config.json -profile=node - | ${CFSSLJSON_BIN} -bare "${prefix}"
   ```

   修改成：

   ```sh
     if [[ ! -r "ca.pem" || ! -r "ca-key.pem" ]]; then
       ${CFSSL_BIN} gencert  -initca ca-csr.json | ${CFSSLJSON_BIN} -bare ca -
     fi
     ...
     #echo "Generate "${prefix}" certificates..."
     echo '{"CN":"'"${prefix}"'","hosts":[],"key":{"algo":"rsa","size":2048},"names":[{"C":"CN","ST":"Shenzhen","L":"Shenzhen","O":"tencent","OU":"'"${prefix}"'"}]}' \
       | ${CFSSL_BIN} gencert  -hostname="${CERT_HOSTNAME},${prefix/-/.}.${ONEX_DOMAIN}" -ca=ca.pem -ca-key=ca-key.pem \
       -config=ca-config.json -profile=node - | ${CFSSLJSON_BIN} -bare "${prefix}"
   ```

   6. 修改代码中定义的版本

      go.mod 的版本是 1.22.0

      项目中的。go-verison: 1.21.4

      在 scripts/lib/golang.sh 文件使用到 .go-version 的版本

      ```sh
      onex::golang::verify_go_version() {
        # default GO_VERSION to content of .go-version
        GO_VERSION="${GO_VERSION:-"$(cat "${ONEX_ROOT}/.go-version")"}"
        if [ "${GOTOOLCHAIN:-auto}" != 'auto' ]; then
          # no-op, just respect GOTOOLCHAIN
          :
        elif [ -n "${FORCE_HOST_GO:-}" ]; then
          # ensure existing host version is used, like before GOTOOLCHAIN existed
          export GOTOOLCHAIN='local'
        else
          # otherwise, we want to ensure the go version matches GO_VERSION
          GOTOOLCHAIN="go${GO_VERSION}"
          export GOTOOLCHAIN
          # if go is either not installed or too old to respect GOTOOLCHAIN then use gimme
          if ! (command -v go >/dev/null && [ "$(go version | cut -d' ' -f3)" = "${GOTOOLCHAIN}" ]); then
            export GIMME_ENV_PREFIX=${GIMME_ENV_PREFIX:-"${LOCAL_OUTPUT_ROOT}/.gimme/envs"}
            export GIMME_VERSION_PREFIX=${GIMME_VERSION_PREFIX:-"${LOCAL_OUTPUT_ROOT}/.gimme/versions"}
            # eval because the output of this is shell to set PATH etc.
            eval "$("${ONEX_ROOT}/third_party/gimme/gimme" "${GO_VERSION}")"
          fi
        fi
      ```

      
