---
title: Git 学习笔记
date: 2015-11-08 11:37:19
tags:
  - Git
categories: Git
banner: /images/2015/11/git.jpg

---

本文记录一下自己在学习、使用Git时不断碰到的问题与解决方法。个人感觉最好的在线教材是 [《Pro Git 2nd Edition（中文版）》](http://git.oschina.net/progit/)。遗憾的是这本书尚无国内出版社出版发行，因此无法享受到纸质书阅读的快乐（除非自己打印了一本 ~~ ）。另外，《Git版本控制管理（第2版）》这本书也很不错，有纸质书可以读。

<!-- more -->

## 基本配置

### 别名

配置别名可以提高工作效率。

```bash
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
$ git config --global alias.st status
```

### 配置用户信息

```bash
$ git config --global user.name "zilong-thu";
$ git config --global user.email "809052335@qq.com"
```

### 生成公钥（SSH keys）

可以用下面的命令生成自己的公钥（public key）：

```bash
# Creates a new ssh key using the provided email
$ ssh-keygen -t rsa -C "xxxxx@xxxxx.com"
```

生成的 public key 在 `~/.ssh/id_rsa.pub` 这个文件里。

## fetch 操作
fetch 操作仅仅是把远程仓库的代码下载当本地而已。可以同时指定相应的本地分支名，git 便会在 fetch 完成后自动创建一个本地分支，追踪该远程分支。例如：

```bash
$ git fetch origin <branch_name>:<local_branch_nam>
```

## pull 操作

### 强制更新

[Git Pull While Ignoring Local Changes?](http://stackoverflow.com/questions/4157189/git-pull-while-ignoring-local-changes)

【方法1】先clean当前的工作目录，使其与上一次的commit一致，然后再pull即可

```bash
$ git reset --hard
$ git pull
```

【方法2】先将改动们暂存一下（`stash`），然后更新，更新完了再将暂存栈弹出或删除。

```bash
$ git stash
$ git pull
$ git stash pop
```

## 提交操作

### 撤销刚才的提交
好比，初始化一份nodejs代码，`git commit` 完了，才想起来没有把 `node_modules` 目录加进 `.gitignore`文件里去，太可怕了。得把刚才的提交撤销掉，而且保证 `node_modules` 目录不能在代码仓库里。就得用到这个命令了：

```bash
$ git reset --hard HEAD~1
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

`--hard`是物理删除，直接把工作区的改动都删掉了。

### 修改刚才的提交的注释

```bash
$ git commit --amend
```

这样会打开文本编辑器，可以让你修改刚才的提交的注释。

### 更改提交历史——交互式变基

交互式变基，即指 `git rebase -i <commit-id>`，然后自该 hash 之后（不含该 hash）的所有提交记录就会被呈现在文本编辑器里，按照提示编辑此文本，保存后即可实现对相应提交对象的修改。

很明显，任何对提交历史的操作，都不应该在共有分支上进行。请确保此分支为私有。

### 移动分支中的某些提交

假如最开始的分支图是这样的：分支 server 是从 master checkout 出来的，还有一个分支 client 是从 server 的 D 提交处 checkout 出来的，

```
  master
    ↓
A-B-C       server
     \       ↙
      C-D-E-F
         \
          G-H
            ↑
          client
```

那么执行 `git rebase --onto master server client` 命令，会取出 client 分支，找出处于 client 分支和 server 分支的共同祖先之后的修改中的、既与 master 不同、又与 server 分支不同的那些提交，单独拿出来在 master 上面重演一遍。于是就会得到这样的提交图：

```
  master   client
    ↓      ↙
A-B-C-G'-H'
     \
      C-D-E-F ← server
```

执行这样的命令时，最好相应的提交都是“原子性”的。


### 提交标签

可以为某个提交创建标签：

`git tag -a <tag_name> <commit_id>`

可以像下面这样，单独推送该标签到远程仓库：

`git push origin <tag_name>`

其他推送标签的方法可以参考：http://stackoverflow.com/questions/5195859/push-a-tag-to-a-remote-repository-using-git


## 分支操作

### 查看分支
`git branch`，查看已有的本地分支

`git branch -a`，查看全部分支，包括本地分支和远程追踪分支

`git branch -r`，只查看远程追踪分支

### 删除本地分支
`git branch -d branch_name`，适用于要删除的分支中的所有数据都已经合并到其他分支里的情形。

`git branch -D branch_name`，无视该分支下的未合并的提交，强制删除。

### 删除远程分支

`git push origin --delete <branch_name>`，可以删除远程仓库里的分支。删除后，会在网站提示诸如此类的消息：

> No branch or tag 'refs/heads/<branch_name>' exists in repository '...'

### 重命名一个本地分支

使用`-m`修饰符，表明要重命名一个分支（`m`相当于Linux下的`move`，或者`mv`的缩写，`mv`命令在Linux里用于重命名文件）

`git branch -m <oldname> <newname>`，修改任意本地分支的名字

`git branch -m <new_name_for_the_current_branch>`，修改当前所在分支的名字

### 跟踪远程分支

如果一个分支尚未与相应的远程分支建立跟踪关系，可以在 `push` 的时候顺便指定一下：

```
git push --set-upstream origin master
```

意思是，向远程（origin）分支（master）提交代码，并且今后把这个远程分支作为当前分支的远程跟踪分支。以后运行 `git status` 就会提示当前分支是否落后于远程跟踪分支了。

Git 的提示：

> The current branch master has no upstream branch.
>
> To push the current branch and set the remote as upstream, use
>
>    git push --set-upstream origin master

## 储藏

`git stash` 命令会捕获当前分支的工作目录和索引的全部状态，将其存储在一个栈中。

不想提交，又想获取最新的远程仓库代码时：

```bash
$ git stash
$ git pull --rebase origin feature/my_feature_1
$ git stash apply
```

`git stash` 默认执行的是 `git stash save`，可以给这个储藏添加一条备注：

```bash
$ git stash save "这是一个暂存"
```

`git stash apply` 只会取出储藏栈最上面的那个状态，并不会删除它。想要达到“弹出栈”的效果，可以使用 `git stash pop`，这条命令会把储藏栈的顶端状态弹出，并将其从栈中删除。

`git stash list`， 查看暂存栈。里面编号为 0 的是最新的暂存状态。

`git stash clean`， 清空暂存区


## 裸版本库

【待学习补充】

## 提交历史

### 基本命令

`git log`，打开默认的文本编辑器，在其中显示当前分支的提交历史。

`git log <branch_name>`，显示某个分支的提交历史。

`git log -p`，会给出提交历史，并且包含了每次提交的逐行增删改对比。

### 可视化提交历史

git 本身提供了一个可以很好地画出提交分支图历史的命令。

```bash
$ git log --pretty=format:"%h %s" --graph

# `--pretty` 参数可以指明显示提交记录的格式。其值可以是 `oneline` | `short` | `full` | `fuller` | `format`。其中 `format` 可以指定一个自定义格式。
# `--graph` 参数用来指示 git 借助 ASCII 字符来绘制分支、合并历史。
```

上面这个命令可以输出类似这样的字符图：

<img src="/images/2017/05/git-log-graph.png">


### 通过提交信息查找提交历史

这对应一个比较少的场景。

> 产品经理说：“以前我不小心砍掉了一个功能，现在我想让它再次上线，以前的代码应该还在，所以恢复起来不难吧？”

OK，不管难不难，首先得在广袤的代码仓库中找到相关的提交历史。

这个时候，需要关键字。而用户能够用关键字标识提交对象的地方，就只有每次的提交信息（commit message）了。所以说，以后每个特定的功能开始、完成，最好都在提交历史中加入能够描述该功能的关键字。以便日后进行搜索定位。好了，下面说一下如何搜索历史。

猜测那个功能的关键字，是“用户行为分析”。那么可以这样：

> To search the commit log (across all branches) for the given text:
>
> git log --all --grep='用户行为分析'
>
> 参考 [How to search a Git repository by commit message?](http://stackoverflow.com/questions/7124914/how-to-search-a-git-repository-by-commit-message)

OK，真的出来了几个提交记录。那么想查看对应的记录快照是否有相应的代码，应该先创建一个分支，然后

`git checkout <commit-id>`，就会把该提交快照全部从仓库里拿到工作区域。

这时候，可以查看当前的 HEAD 是否为希望的提交记录的 ID ：`git rev-parse HEAD`。

### git diff

```bash
# 对比两次提交之间的差异
$ git diff <commit-id-1> <commit-id-2>

# 对比两个分支之间的代码差异
# 给出 b 相对于 a 的差异
$ git diff branch-a branch-b
```

可以对比两次提交之间的全部差异。例如 `git diff HEAD~ HEAD` 就可以看到所在分支的最近一次提交与之前一次提交的差异。（HEAD只是最近一次提交的 commitID 的别名）

### 找到某一行的全部改动历史

`git blame -L <rowNumber>,<row increment number>`


## checkout

`checkout` 可以用于切换分支，也可以用于从某次体积记录中把某个或该提交的全部文件拿到工作目录中。

```bash
$ git checkout <branch-name>
$ git checkout <commit-id>
$ git checkout <commit-id> path/to/file
```

还可以用于从某个分支里取出其最新的某个特定文件，即 git-checkout specific files from another branch：

```bash
$ git checkout <branch-name> -- <file-name>
```

参考阅读：[Reset or revert a specific file to a specific revision using Git?](http://stackoverflow.com/questions/215718/reset-or-revert-a-specific-file-to-a-specific-revision-using-git)

---------------------------


## 【附录】自己不是很熟练的 Git 命令手册


```bash
# 查看关于某条命令的帮助
$ git --help <command>

# 检查多余的空白（以免给同伴们造成麻烦）
$ git diff --check

# 彻底放弃最近的 N 次提交
$ git reset --hard HEAD~N

# 图形化查看一个文件的提交历史
$ gitk <filename>

# 提交记录，带每次的更改状态
$ git log --stat

# 删除一个远程分支（对 1.7.0 版本及以上）
$ git push origin --delete branch_to_delete

# 创建一个新分支，让它与远程分支的名称一样，并且保持与该远程分支相关
# 这样就创建了一个名为 feature/branch_1 的本地新分支
$ git fetch origin feature/branch_1
$ git checkout --track origin/feature/branch_1
```
