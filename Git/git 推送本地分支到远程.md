# git 推送本地分支远程

### 创建新的分支

```sh
git check -b new_branch
```

### 修改代码提交

```sh
git add .
git commit -am "new branch"
```

### 本地分支: 远程分支

```sh
git push origin

# 如果远程分支不存在，会自动创建
git push origin new_branch:new_branch
```

