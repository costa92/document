# svn 命令
```sh
svn ci -m "COMMIT MESSAGE"
```

更新 ：
```sh
svn up
```

svn log 命令怎么样能够只显示前面几行
```sh
svn log | head -n X
```

删除文件：
```sh
svn delete test.cpp
svn ci -m "svn  deleted "
```

获取远程svn数据
```sh
svn co 地址
```
一次性增加所有新增的文件到svn库：
```sh
svn st | awk '{if ($1 == "?") {print $2} }' | xargs svn add
```

一次性从svn库删除所有需要删除的文件
```sh
svn st | awk '{if ($1 == "!") {print $2}}' | xargs svn rm
```

最后直接提交你的修改(注意：这里的-F 代表上传的注释从comment.txt文件读取)
```sh
svn ci -F comment.txt(svn ci -m "COMMIT MESSAGE")
```

svn: E155015: 提交失败(细节如下) 解决办法
Posted by simapple on Thursday, 18 July 2013
svn 出现冲突是经常发生的事，最近改用命令操作svn，用界面电脑有些反应慢
出现冲突使用svn 命令肯定也是可以解决的：
查看警告信息提示冲突的文件，执行
```sh
svn resolved <文件名>
```
如果没有报错，就证明冲突已解决，再次提交就可以解决问题
```sh
svn add file|dir -- 添加文件或整个目录
svn checkout -- 获取svn代码
svn commit  -- 提交本地修改代码
svn status    -- 查看本地修改代码情况：修改的或本地独有的文件详细信息
svn merge   -- 合并svn和本地代码
svn revert   -- 撤销本地修改代码
svn resolve -- 合并冲突代码
svn help [command] -- 查看svn帮助，或特定命令帮助
```

查看文件详细信息 svn info path
```sh
 eg: svn info tzsys/
```

查看log日志信息 svn log path
```sh
eg:svn log tzsys
```
```sh
svn diff; #什么都不加，会坚持本地代码和缓存在本地.svn目录下的信息的不同;
svn diff -r 3;  #比较你的本地代码和版本号为3的所有文件的不同;
svn diff -r 3 text.c;  #比较你的本地代码和版本号为3的text.c文件的不同;
svn diff -r 5:6;  #比较版本5和版本6之间所有文件的不同;
svn diff -r 5:6 text.c;  #比较版本5和版本6之间的text.c文件的变化。
查看修改的信息使用 log 指令，如下：
svn log;  #什么都不加会显示所有版本commit的日志信息;
svn log -r 4:5;  #只看版本4和版本5的日志信息;
svn log test.c;  #查看文件test.c的日志修改信息;
svn log -v dir;  #查看目录的日志修改信息,需要加v;
查看某个版本的某个文件内容，使用cat指令，如下：
svn cat -r 4 test.c;  #查看版本4中的文件test.c的内容,不进行比较;
```