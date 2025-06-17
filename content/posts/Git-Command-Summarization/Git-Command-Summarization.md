---
date: '2025-06-17T17:11:20+01:00'
draft: false
title: 'Git Command Summarization'
---
# Git 创建仓库并同步到远程仓库
### 初始化
```bash
git init
```
### 添加仓库
```bash
git remote add origin git@github.com:username/xxx.git
git remote remove origin
git remote set-url <REMOTE-NAME> <NEW-URL>
git remote rename <old-name> <new-name>
```
### 查看远程
```bash
git remote -v / git remote remove origin
```
### 创建新分支
```bash
git branch 查看本地分支
git branch -a 查看远程、本地所有分支
git branch -m new_branch_name
```
### 创建空分支
```bash
git checkout --orphan new_branch_name
git reset & git clean -fdx
git commit --allow-empty -m "Initial commit"
```
### 提交仓库
```bash
git status

git add .
git commit -m 'commit_message'
git push/pull origin local_branch:remote_branch
```

# 分支操作
```bash
git push -u origin <remote_branch_name>
git push --set-upstream origin <remote_branch_name> # 设置本地分支关联的远程分支
git checkout <branch_name> # 切换本地分支
git checkout -b <branch_name> # 创建一个本地远程分支
git branch -v # 查看本地分支信息
git branch -r # 查看远程分支
git branch -a # 查看全部分支
git branch -vv # 查看关联分支信息
```
远程仓库多分支条件下，git clone只会克隆主分支。如果想要克隆其他分支：
```bash
git clone <仓库URL> –branch <分支名> –single-branch
```
本地仓库未跟踪远程分支，可以使用以下命令手动关联分支：
```bash
git branch -r
git checkout -b <本地分支名> origin/<远程分支名>
```