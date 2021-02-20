# Git

## 调取远程仓库

### 1.git remote:查看和绑定远程仓库

```shell
git remote -v 查看远程仓库详细信息
git remote add <远程仓库名字> <远程仓库url> 绑定远程仓库地址
git remote set-url <远程仓库名字> <远程仓库url>
```

### 2.拉取远程仓库资源

```
git fetch <远程仓库名称> <远程仓库分支名称>:<本地分支>
//作用:将远程仓库分支的资源下载然后再本地新建一个本地分支，保存在这个temp分支中；**不合并**
git pull <远程仓库名称> <远程仓库分支名称>:<本地分支>
//同上，但是区别就是拉取分支后,自动合并:其中" :<本地分支> "可以省略；
```

```
git fetch origin master : temp 
//将远程仓库保存在temp中
git merge temp
//将temp分支合并到当前所再分支上。

其实上两部就相当于 git pull origin master
```

### 3.git push :推送分支

```
git push <远程仓库名称> <远程仓库分支>
//将本地分支推送到远程分支
```

### 4.切换分支

```shell
git switch -c <newBranch>//创建并且切换到newbranch上
git switch -m <branch> //切换分支 并且merge一下
git switch -q <branch> //静默切换分支
git checkout <branch>//切换分支
git checkout -b <branchname> //创建分支
git branch -d <branch> //删除分支
git branch <branchname> //创建分支
git branch #不加任何参数 会返回当前所有分支的一个列表 参数：-v：查看最后一次的提交
git branch --merge  OR --no-merge  #查看那个哪些分支已经合并到当前分支 

```

### 5.合并分支

```
git merge <分支名称>  #合并分支
```







