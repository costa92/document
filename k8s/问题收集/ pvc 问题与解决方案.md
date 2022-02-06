# pvc 问题与解决方案

### 删除pvc后，pvc的状态一直处于terminating

直接将pvc的yaml给delete了，执行完了之后，发现有四个pvc一直处于terminating状态，一直删不掉；尝试kubectl delete pvc pvc-name -n命名空间也没有删除掉；
杀不掉的pvc是被多个pod使用的；之所以杀不掉，是因为还有别的正在运行的pod在使用它；

解决：
 将所有有关系的pod都停止服务，pvc就杀掉了；