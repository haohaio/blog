---
title: Git 常用命令
entitle: 'git-common-commands'
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 其它
timestamp: 1544875401
comments: true
date: 2018-12-15 20:03:21
tags:
- git
keywords:
description:
photos:
- /img/2018/git.png
---

## 配置

```bash
# 配置git的全局账号
$ git config --global user.name "username"
# 配置git的全局账号邮箱
$ git config --global user.email "email"
# 查看配置的结果
$ git config --global -l
```

## 仓库

```bash
# 新建本地仓库
$ git init
# 克隆远程仓库
$ git clone url (gcl)
# 查看所有远程仓库信息
$ git remote -v
# 添加远程仓库
$ git remote add name url
# 删除远程仓库
$ git remote remove name
```

## 基本命令

- git add

```bash
# 提交所有Working Dir修改到Index
$ git add .
# 提交所有Working Dir中dir目录/file文件的修改到Index
$  git add dir/file
```

```bash
# "git add ." 和 "git add -A" 的区别?
# stages new and modified, without deleted
$ git add .
# stages all
$ git add -A
```

- git commit

```bash
# 提交所有Index修改到HEAD，并附上 commit message
$ git commit -m
# 提交所有修改到HEAD(包含Working Dir和Index，但不包含new files)，并附上 commit message
$ git commit -a -m (gcam)
# 修订HEAD的修改
$ git commit --amend
```

- git push

```bash
# 提交本地仓库修改至Remote仓库的master分支
$ git push origin master
```

- git reset

```bash
# 从Index中恢复所有修改
$ git reset
# 恢复到HEAD的上一次提交
$ git reset HEAD^(HEAD~1)
```

- git checkout

```bash
# 撤销相应的文件修改
$ git checkout file(dir) : 
# 检出某一commit ID的修改
$ git checkout d928a3
```

- git clean

```bash
# 删除untracked files
$ git clean -f
# 连 untracked 的目录也一起删掉
$ git clean -fd
# 查看哪些文件会被删除
$ git clean -nfd
```

- git diff

```bash
# 查看Working Dir与Index的区别
$ git diff
# 查看Index与HEAD的区别
$ git diff –-cached
# 查看Working Dir与HEAD的区别
$ git diff HEAD
```

- git log

```bash
# 显示 commit log
$ git log
# 显示代码差异
$ git log -p
# 概要显示
$ git log --stat
# 单行显示
$ git log --pretty=oneline
# 显示最近3条提交
$ git log -3
# 显示最近24h的提交
$ git log --since="24 hours"
# 显示某file文件修改的记录
$ git log file
# 通过信息检索提交
$ git log --grep="fixbug"
# 通过作者检索提交
$ git log --author="yuanfang"
```

- git reflog

> 会记录所有HEAD的历史，也就是说当你做 reset，checkout等操作的时候，这些操作会被记录在reflog中。

- git fsck --lost-found

> 查看“丢失的”对象们，比如因reset而看不到的commit

- git show

```bash
# 查看HEAD的所有代码改动
$ git show
# 查看某一commit ID的所有代码改动
$ git show d928a3
```

- git blame

```bash
# 查看file文件每一行的最近一次修改的信息  
$ git blame file
# 查看file文件50行至60行的最近一次修改的信息
$ git blame -L 50,60 file
```

- git stash

```bash
# 将修改暂存入栈
$ git stash
# 恢复栈顶的修改
$ git stash pop
# 列出栈中所有修改
$ git stash list
# 列出栈中所有修改的代码详情
$ git stash list -p
# 恢复栈中指定修改
$ git stash apply stash@{1}
# 清空栈
$ git stash clear
# 删除栈中修改
$ git stash drop stash@{1}
```

## 分支

- git branch

```bash
# 列出本地已经存在的分支
$ git branch
# 列出远程分支
$ git branch -r
# 列出本地分支和远程分支
$ git branch -a
# 创建新的分支new_branch
$ git branch new_branch
# 切换到分支new_branch
$ git checkout new_branch
# 删除分支new_branch
$ git branch -d new_branch
# 推送new_branch到Remote仓库
$ git push origin new_branch
# 删除Remote仓库new_branch
$ git push origin :new_branch
```

- git merge & git rebase & git cherry-pick

```bash
# 合并master到当前分支
$ git merge master
# 变基当前分支到master
$ git rebase master
# 合并某个commit到当前分支
$ git cherry-pick commit-id
```

## 标签

```bash
# 列出本地已经存在的标签
$ git tag
# 创建新的标签new_tag
$ git tag new_tag
# 删除标签new_tag
$ git tag -d new_tag 
# 推送new_tag到Remote仓库
$ git push origin new_tag
# 推送所有标签到Remote仓库
$ git push origin --tags
# 删除Remote仓库new_tag
$ git push origin -d tag new_tag
$ git push origin :refs/tags/new_tag
# 获取Remote仓库所有的标签
$ git fetch origin
```

## 补丁

```bash
# 将修改写入到patch文件 .patch
$ git diff > .patch
# 将.patch的修改恢复到当前git工程
$ patch -p1 < .patch
```

## .gitignore

- 配置规则

```bash
*.a       # 忽略所有 .a 结尾的文件
!lib.a    # 但 lib.a 除外
/TODO     # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/    # 忽略 build/ 目录下的所有文件
doc/*.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

- .gitignore文件发现却没有生效时（ .gitignore只能忽略那些原来没有被track的文件）

```bash
$ git rm -r --cached .
$ git add .
$ git commit -m 'update .gitignore'
```

- 统计某人提交代码的次数

```bash
$ git log --pretty=oneline --author='username' | wc -l
```