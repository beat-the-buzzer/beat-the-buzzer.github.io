---
title: Git撤销操作汇总
date: 2020-07-30
tags: [Git,GitHub]
categories: 
- 其他
---

在任何一个阶段，你都有可能想要撤消某些操作。Ctrl ACV 我们很常用，Ctrl Z 使用频率也很高。同理，Git上的操作我们也经常需要去撤销一些操作，下面就简单总结一下。

### 撤销修改

撤销修改，其实就是把代码回退到没有修改之前的状态，其实就是VSCode上面的那个撤销按钮。不过VSCode在内部做了判断：如果本次修改是新增一个文件，那么撤销按钮点击后，就执行删除操作；如果本次修改是删除或者修改了一个文件，点击撤销按钮的时候，就是从本地仓库里恢复文件；

![撤销修改](https://gitee.com/beat-the-buzzer/pictures/raw/master/git/git01.png)

```bash
git checkout [filename]
```

这里要注意的事情就是checkout这个命令，我们熟知的是用于切换分支，所以对于撤销修改这个操作，我们可能不太熟悉。

### 撤销暂存

这个在VSCode里面也有自带的快捷按钮，这个命令可以说是git add 的反向操作，就是把文件从暂存区里面拿出来。

![撤销暂存](https://gitee.com/beat-the-buzzer/pictures/raw/master/git/git02.png)

```bash
git reset HEAD [filename]
```

### 提交信息的撤销

```bash
git commit --amend
```

这个命令也算是一种撤销，只不过撤销的是提交信息。比如，我们修改了一个bug，改了3个文件，并且已经提交了。突然我们发现漏提交了一个文件，这个时候，假设我们走正常的提交流程，就会多创建了一个提交，我们就不能一次性地看到这个bug修改了哪些文件。此时我们可以使用上面这个命令，可以编辑上一次提交的日志，也可以说是撤销重新提交。

### 提交到本地仓库之后的回退

两种回退方式：reset和revert。

reset回滚的时候，可以看做版本指针移动到指定位置，这个位置后面的提交都会被丢失；

![reset操作](https://gitee.com/beat-the-buzzer/pictures/raw/master/git/git03.jpeg)

```bash
git reset --hard HEAD^  # 回退到上一个版本
git reset --hard HEAD^^  # 回退到上上一个版本
git reset --hard HEAD~100  # 回退到前100一个版本
git log # 获取提交日志
git reset --hard [commit-hash] # 回退到指定的那次提交
```

revert 是回滚某次提交，创建了一次新的提交，这次提交改动的内容就是还原了那次提交改变的东西。

![revert操作](https://gitee.com/beat-the-buzzer/pictures/raw/master/git/git04.jpeg)

```bash
git log # 获取提交日志
git revert -n [commit-hash] # 这次提交的东西没了
git add # 后续提交
git commit -m ""
```

revert 是回滚某个 commit ，reset是回滚“到”某个 commit 之后的状态

### 推送到远程之后的回退

上面已经使用了reset和revert，接下来我们只需要推送到远程就行了。

如果是reset命令，当前回退后的版本低于线上版本，因此直接提交会报错，此时需要强制提交到远程：

```bash
git push -f
```

如果是revert命令，当前版本是一次新的提交，直接push就可以了：

```bash
git push
```