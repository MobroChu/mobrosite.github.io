---
title: git常用操作命令
date: 2018-05-24 15:22:30
tags: git
description: 本文为自我学习篇，当做自己做的笔记。仅供自己以后翻阅
---

*Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。
Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持。*

下面，请跟着 Mobro 一起了解常用的 git 命令：

### 初始化
``` git
git config --global "user.name"         # 全局配置用户名
git config --global "user.email"        # email
git config user.name/user.email         # 查看用户名/邮箱
ssh-keygen -t rsa -C "mobro_chu@163.com"    # 创建ssh key
    ## 公钥是要放在 github 上，密钥就不必了
ssh -T git@github.com       # 检查连接是否成功
git remote add origin git@server-name:path/repo-name.git    
# 与远程仓库建立连接
# eg: git remote add origin git@github.com:MobroChu/mine.git
```

### git分支相关

``` git
git branch          # 查看当前分支  
git branch <name>   # 创建一个 name 的分支  
git branch -m A B   # 将原来为A的分支，改名成 B  
git branch -m C     # 将当前分支改名为 C
git branch -d E     # 删除分支
git branch -D F     # 强行删除
git branch <branch_name> <hash_value>   # 恢复被删除的分支
git branch --set-upstream branch-name origin/branch-name 
# 建立本地分支和远程分支的关联

git push origin :G  # 删除远程仓库的G分支
git checkout -b A   # 创建A分支，并切换到A分支下
git checkout A      # 从当前分支切换到A分支下
git merge A         # 将A分支合并到当前分支
git merge --no-off -m "合并分支后会生成一个 commit" A
--no-off            # 不使用 fast forward 模式合并分支 
-m ""               # 因为合并分支后会生成一次 commit，所以把描述写上
# 合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。
```

### 版本相关
``` git
git status          # 查看当前仓库状态
git log             # 查看日志
git reflog          # 查看命令记录
git diff <filename> # 查看暂存区filename这个文件与仓库中filename文件的不同
git diff HEAD -- <filename> # 查看工作区和版本库里面最新版本中filename的区别
git reset HEAD <filename>   # 撤销缓存区的修改
git reset --hard HEAD/HEAD~100/版本号   #版本回退
    ## HEAD 指向的是当前版本
    ## HEAD~100 从当前版本向前退100个版本
    ## 版本号 不用全部复制，只需要版本号的前几位就好了
git add             # 将文件添加到要提交的队列中
git commit -m "xxx" # 提交的到暂存区，并添加注释xxx
git commit -am "xxx"       
===>   git add <modify files> , git commit -m "xxx"
git push -u origin A    # 推送到远程仓库
    ## -u 一般情况下，第一次才有，方便以后推送或者拉去，可以简化命令
    ## origin 远程仓库名，可以换，但很少有人去换
    ## A 要推送到A分支下
```

### 常使用到的暂存命令
``` git
git stash           # 添加储藏
git stash push -m "注释内容"
git stash list      # 查看储藏
# 使用 num 这个版本号的 stash， 其中 num 为数字
## pop 会删除 stash 队列中的 num 这个缓存版本，apply 不会。
git stash pop stash@{num}
git stash apply stash@{num}

git stash drop      # 删除储藏
```

### 日志查看
```git
git log --graph --pretty=oneline --abbrev-commit   # 查看分支的合并情况
--graph             # 显示分支合并情况
--pretty=oneline    # 以精简形式显示，oneline 表示一次提交只显示一行
--abbrev-commit     # 提交记录的哈希值不用全部显示
```

### 远程库
```git
git remote          # 查看远程库信息
git remote -v       # 查看远程库详细信息

## 打印的信息
origin  git@github.com:MobroChu/blog.git (fetch)
origin  git@github.com:MobroChu/blog.git (push)
```
上面显示了可以抓取和推送的 origin 的地址。如果没有推送权限，就看不到 push 的地址。

### 其他
``` git
git pull            # 拉取远程当前分支的最新代码
# 把所有的remote变化都拉下来，并且将你最新的commit置于最顶上
git rebase          # 把本地未push的分叉提交历史整理成直线
```

### 标签
```git
git tag A commitid/HEAD         # 新建一个标签，默认为HEAD，也可以指定一个 commitid
git tag -a A -m "blablabla..."  # 指定标签描述信息
git tag             # 查看所有标签

git tag -d A        # 删除标签 A 
git push origin A/--tag   # 推送 A 标签 / 或者全部推送

# 删除远程 tag, 需要以下两步
git tag -d A
git push origin :refs/tags/A # 删除远程

```

还有许多命令，待后续补齐。just 未完待续、

