# kubectl 备忘单

参考连接：https://kubernetes.io/zh/docs/reference/kubectl/cheatsheet/


命令：
```bash 
   # 生产yaml内容，但不执行
   kubectl create deployment nginx --image=nginx  --dry-run=client -oyaml
   # 生产yaml内容并导出到文件中
   kubectl create deployment nginx --image=nginx  --dry-run=client -oyaml > nginx-dp.yaml
```