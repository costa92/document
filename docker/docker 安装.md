# 安装 docker 

###  CentOS 安装

1. 安装 yum 源

   ```
   curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
   
   yum install -y yum-utils device-mapper-persistent-data lvm2
   
   yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
   
   sed -i -e '/mirrors.cloud.aliyuncs.com/d' -e '/mirrors.aliyuncs.com/d' /etc/yum.repos.d/CentOS-Base.repo
   ```

2. 安装 docker

   ```bash
   yum install docker-ce-19.03.* -y
   ```

   温馨提示：

   由于新版kubelet建议使用systemd，所以可以把docker的CgroupDriver改成systemd

   ```
   mkdir /etc/docker
   cat > /etc/docker/daemon.json <<EOF
   {
     "exec-opts": ["native.cgroupdriver=systemd"]
   }
   EOF
   ```

   设置开机自启动Docker：

   ```
   systemctl daemon-reload && systemctl enable --now docker
   ```

   