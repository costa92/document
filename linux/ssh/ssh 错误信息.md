# ssh 错误信息处理

1. 使用SSH连接远程服务器的时候 报出 client_loop send disconnect Broken pipe的错误

    **解决方案:**
    
    **方案 1：**
    
    修改客户端的 /etc/ssh/ssh_config 配置文件
    
    在Host *条目下添加 IPQoS=throughput
    
    再重启 systemctl restart sshd
    
    
    **方案 2：**
    
    临时解决可以再命令行中加入-o 'IPQoS=lowdelay throughput'参数即可