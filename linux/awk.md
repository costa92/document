# awk 常用的命令

## 处理字符串

**提取字符串第三字符串**

```sh
echo 'this is a test' |awk '{print $3}'
```





**替换 字符串中一个**

```sh
 echo "Hello Tom" | awk '{$2="Adam"; print $0}'
```

输出：

```sh
Hello Adam
```





### 利用变量    [菜鸟]( https://www.runoob.com/w3cnote/8-awesome-awk-built-in-variables.html)   

1. $NF  表示当前行有多少个字段，

   因此`$NF`就代表最后一个字段。

```sh
echo 'this is a test' | awk '{print $NF}'
```

输出：

```sh
test
```

​     `$(NF-1)`代表倒数第二个字段

```sh
echo 'this is a test' | awk '{print $(NF-1)}'
```

输出：

```sh
a
```

   2 .变量`NR`表示当前处理的是第几行。



## 处理文件

处理文件 logs.txt , 文件内容:

```sh
07.46.199.184 [28/Sep/2010:04:08:20] "GET /robots.txt HTTP/1.1" 200 0 "msnbot"
123.125.71.19 [28/Sep/2010:04:20:11] "GET / HTTP/1.1" 304 - "Baiduspider"
```

 **获取第一列数据**

```sh
awk '{print $1}' logs.txt
```

输出：

```sh
07.46.199.184
123.125.71.19
```

**这个文件的字段分隔符是冒号（`:`），所以要用`-F`参数指定分隔符为冒号。然后，才能提取到它的第一个字段。**

```sh
awk -F ':' '{print $1}' logs.txt
```

输出：

```sh
07.46.199.184 [28/Sep/2010
123.125.71.19 [28/Sep/2010
```



**提取log 的时间**

```sh
awk '{print $2}' logs.txt | awk 'BEGIN{FS=":"}{print $1}' | sed 's/\[//'
```

输出:

```sh
28/Sep/2010
28/Sep/2010
```



**统计某一个字段的相加 ** 

每次都大于结果

```sh
awk '{a+=$(NF-2); print "Total so far:", a}' logs.txt
```

```sh
Total so far: 200
Total so far: 504
```

执行完后在打印结果

```sh
awk '{a+=$(NF-2)}END{print "Total:", a}' logs.txt
```

输出：

```sh
Total: 504
```



###  利用变量 

1. **OFS: 输出字段分隔符变量**

**OFS**(Output Field Separator) 相当与输出上的 **FS**, 默认是以一个空格字符作为输出分隔符的，下面是一个 **OFS** 的例子:

正常空格命令：

```sh
awk  '{print $1, $3;}' logs.txt
```

输出结果：

```sh
07.46.199.184 "GET
123.125.71.19 "GET
```

注意命令中的 print 语句的, 表示的使用一个空格连接两个参数，也就是默认的OFS的值。因此 **OFS** 可以像下面那样插入到输出的字段之间:

```sh
awk 'BEGIN{OFS="=>";}{print $1, $3;}' logs.txt
```

输出：

```sh
07.46.199.184=>"GET
123.125.71.19=>"GET
```

注意： GET 前面多一个双引号，我们需要去掉

```sh
awk  '{print $1, $3;}' logs.txt |sed 's/\"//'

awk 'BEGIN{OFS="=>";}{print $1, $3;}' logs.txt |sed 's/\"//'
```

输出：

```sh
07.46.199.184 GET
123.125.71.19 GET

07.46.199.184=>GET
123.125.71.19=>GET
```



2. **变量`NR`表示当前处理的是第几行。**

```sh
awk '{print NR") "$1}' logs.txt
```

输出:

```sh
1) 07.46.199.184
2) 123.125.71.19
```

3. 变量 NF  表示一列的最后一个字段 

   $(NF-2) 表示倒数第三个字段    

   利用 if 判断倒数第三个字段 是否 等于  200

   ```sh
    awk '条件 动作' 文件名
   ```

```sh
awk '{if ($(NF-2) == "200") {print $0}}' logs.txt
```

输出：

```sh
07.46.199.184 [28/Sep/2010:04:08:20] "GET /robots.txt HTTP/1.1" 200 0 "msnbot"
```







## 处理进程

1. 根据 lsof 命令获取进程 PID

```sh
lsof -i:3100|awk 'NR>1' |awk '{print $2}' 
```

  kill  -9 进程

```sh
lsof -i:3100|awk 'NR>1' |awk '{print $2}'  | xargs kill -9
```



NR  大于  1 表示从第二行开始

输出：

```sh
2299757
2299757
2299757
2299757
2299757
2299757
2299757
2299757
2299757
2410946
```



参考链接：

[Awk Example](https://gregable.com/2010/09/why-you-should-know-just-little-awk.html)

[菜鸟](https://www.runoob.com/linux/linux-comm-awk.html)

[awk 入门教程](https://www.ruanyifeng.com/blog/2018/11/awk.html)

[30 Examples for Awk Command in Text Processing](https://likegeeks.com/awk-command/)



[查找grep、提取awk、sed、重定向](https://zhuanlan.zhihu.com/p/34946663)
