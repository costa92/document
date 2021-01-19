### 常用的grep的命令

1. 提取文档中的未加 “#” 号的文字
```shell
 grep -v -e '#' -e ^$ cluster.yaml
```


