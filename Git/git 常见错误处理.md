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
```

