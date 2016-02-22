---
title: Git 学习笔记
date: 2015-11-08 11:37:19
tags: 
  - Git
categories: Git
banner: /images/2015/11/git.jpg

---

本文记录一下自己在学习、使用Git时不断碰到的问题与解决方法。个人感觉最好的在线教材是 [《Pro Git 2nd Edition（中文版）》](http://git.oschina.net/progit/)。遗憾的是这本书尚无国内出版社出版发行，因此无法享受到纸质书阅读的快乐（除非自己打印了一本~~）。另外，《Git版本控制管理（第2版）》这本书也很不错。有纸质书可以读。

<!-- more -->

## pull 操作

### 强制更新

[Git Pull While Ignoring Local Changes?](http://stackoverflow.com/questions/4157189/git-pull-while-ignoring-local-changes)

【方法1】先clean当前的工作目录，使其与上一次的commit一致，然后再pull即可

```
git reset --hard
git pull
```

【方法2】先将改动们暂存一下（`stash`），然后更新，更新完了再将暂存栈弹出或删除。

```
git stash
git pull
git stash pop
```

## 提交操作

### 撤销刚才的提交
好比，初始化一份nodejs代码，`git commit` 完了，才想起来没有把 `node_modules` 目录加进 `.gitignore`文件里去，太可怕了。得把刚才的提交撤销掉，而且保证 `node_modules` 目录不能在代码仓库里。就得用到这个命令了：

```
git reset --hard HEAD~1
```

然后，把事情做正确了，再提交。当然了，更全面的问题与解决方案在这里：

+ [How do you undo the last commit? | 来自 Stackoverflow 的问答](http://stackoverflow.com/questions/927358/how-do-you-undo-the-last-commit)。

**原理**

不想使 `node_modules` 再在代码仓库里出现，过程是这样的：

```
# 出错的提交，是这样的，F 代表当前的文件：
   (F)
A-B-C
    ↑
  master
 
# 执行这个
git reset --hard HEAD~1
 
# 当前代码仓库就成了这样：
 (F)
A-B
  ↑
master
```

### 修改刚才的提交的注释

```
git commit --amend
```

这样会打开文本编辑器，可以让你修改刚才的提交的注释。

## 分支操作

### 删除分支
`git branch -d branch_name`，适用于要删除的分支中的所有数据都已经合并到其他分支里的情形。

`git branch -D branch_name`，无视该分支下的未合并的提交，强制删除。

### 重命名一个本地分支

使用`-m`修饰符，表明要重命名一个分支（`m`相当于Linux下的`move`，或者`mv`的缩写，`mv`命令在Linux里用于重命名文件）

`git branch -m <oldname> <newname>`，修改任意本地分支的名字

`git branch -m <new_name_for_the_current_branch>`，修改当前所在分支的名字

---------------------------

 
## 【附录】自己不是很熟练的 Git 命令手册


```
# 查看关于某条命令的帮助
git --help <command>
 
# 检查多余的空白（以免给同伴们造成麻烦）
gid diff --check
 
# 彻底放弃最近的 N 次提交
git reset --hard HEAD~N
 
# 图形化查看一个文件的提交历史
gitk <filename>

# 提交记录，带每次的更改状态
git log --stat

# 暂存
# 不想提交，又想获取最新的远程仓库代码时
git stash
git pull --rebase origin feature/my_feature_1
git stash apply

# 查看暂存栈
git stash list

# 清空暂存区
git stash clean

# 删除一个远程分支（对 1.7.0 版本及以上）
git push origin --delete branch_to_delete

# 创建一个新分支，让它与远程分支的名称一样，并且保持与该远程分支相关
# 这样就创建了一个名为 feature/branch_1 的本地新分支
git fetch origin feature/branch_1
git checkout --track origin/feature/branch_1
```
