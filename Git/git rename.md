## **1、本地分支重命名**

```
 git branch -m oldName  newName
```

## **2、将重命名后的分支推送到远程**

```
git push origin newName
```

## 3、删除远程的旧分支

```
git push --delete origin oldName
```

### 显示如下，说明删除成功

```
To http://11.11.11.11/demo/demo.git
 - [deleted]           oleName
```

```sh
git push ---set-upstream origin newName
```

