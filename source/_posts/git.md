---
title: Git基本用法总结
date: 2019-11-29
tags: [Git,GitHub]
categories: 
- 其他
---

### 用户配置

查看当前配置的用户名和邮箱：

```shell
git config user.name
git config user.email
```

配置用户名和邮箱：

```shell
git config --global user.name 'name'
git config --global user.email 'email'
```

密码操作：

```shell
git config --global credential.helper store 
```

### 新建操作

新建分支的操作：

```shell
git branch [mybranch] 创建本地分支
git checkout [mybranch] 切换本地分支
git checkout -b [mybranch] 创建并切换分支
git checkout -b [mybranch] origin/[分支名] 从之前的某个分支里拉一个分支出来，也就是说，这里复制了某个分支的代码
git push origin [mybranch] 本地推送到远程（同时创建了远程分支）
git subtree push --prefix=dist origin gh-pages 将文件前缀为dist的文件夹内容推送到gh-pages远程
```

### 查看操作

查看当前文件状态：

```shell
git status
```

查看本地分支：

```shell
git branch
```

查看远程分支：

```shell
git branch -a
```

查看远程地址： 

```shell
git remote -v
```

### 提交代码

```shell
git add [文件名]
git reset HEAD // 撤销上次add
git commit -m '[提交日志]'
git reset --soft HEAD^ // 撤销上次提交
git push
```

### 分支合并的操作

```shell
git merge [test] (至当前分支)
```

分支合并之前的操作：保证test分支是最新的，切换到test分支，

```shell
git checkout [test] 切换到test分支
git fetch 获取更新（一般的图形工具都会有自动fetch的功能）
git pull 拉取代码
```

然后切换到当前分支，合并test至当前分支。

### 版本回退的操作

```shell
git log 找到需要回退到这个版本的commit ID
git reset --hard [commit id]
git push -f -u origin [branchname]
```

最好不要在需要回退的版本上直接操作，应该先在那个分支上面拉一个分支，然后在分支上进行操作。

### 删除分支的操作

```shell
git push origin --delete [mybranch] // 删除远程分支
git branch -d [mybranch] // 删除本地分支 
```


