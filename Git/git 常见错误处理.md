# git 常见的错误处理

1、fatal: Not possible to fast-forward, aborting.

出现的原因：

两个分之同时改了同样的地方，造成冲突。一般的情况使用 merge  合并分支

```sh
git checkout master
git merge dev
```

如果出现代码冲突的时候需要手动解决，但是有时候是无法合并成功的。

解决:

```sh
git checkout master
git rebase dev
```



如果是一个分支，但是代码出现修改，也会出现改错误。

解决:

```sh 
git pull origin master --rebase

git pull origin development --rebase

git pull origin test --rebase
```

**注意** 修改冲突代码

在提交代码

```sh
git push origin HEAD:develop     // develop 是分支
```


在冲突解决完毕并且提交代码后，执行下面的命令：
```sh
// 在终端也会有需要执行这个命令的提示
git rebase --continue
```