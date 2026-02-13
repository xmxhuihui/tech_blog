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
git branch # 查看本地分支
git branch -a # 查看远程、本地所有分支
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
### 删去远程仓库中的文件，但是本地保留
如果以前提交过一些文件到远程仓库，但现在想删去的话：
1. 先在.gitignore中添加文件/文件夹路径
2. ```git rm --cached <path>```删去git对该路径文件的跟踪
3. git commit
4. git push

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

# Q: 万一以后不小心 add/commit 了大文件，如何避免 push 到 GitHub
## 1️⃣ 还没 push（只是 add 或 commit）
### 情况 A：只 add 到暂存区
``` bash
git reset path/to/big.file
git restore --staged path/to/big.file
```
这会把文件从暂存区移除，但保留工作区文件。
### 情况 B：已经 commit（还没 push）
```bash
git reset --soft HEAD~1
git restore --staged path/to/big.file
```
撤回多条提交：
```bash
git reset --soft HEAD~N
git restore --staged path/to/big.file
```
要从暂存区中移除所有文件：
```bash
git restore --staged .
```
然后可以重新add并提交了。以后记住add之前先用git status，查看要提交的文件中，大文件是否已经放入了.gitignore文件中。
## 2️⃣ 合并最后几条提交
<span style="color:red">使用这个方法之前，一定要注意，如果你有其他文件没保存在git上，千万别rebase，东西会丢。铁律就是，rebase之前一定git status的```working tree clean```，否则绝对不要rebase。执行这个命令之前最好在本地备份，比如copy或者做一个压缩包</span>

使用 交互式 rebase：
```bash
git rebase -i HEAD~N
```

这个是恢复到合并最后多条提交，但项目是最后一条提交的版本

## 3️⃣ 已经 commit 并 push（历史里有大文件）
GitHub 会拒绝 push 超过 100MB 文件，如果不想 push：
### 方法 A：从历史彻底删除大文件
```bash
pip install git-filter-repo
git filter-repo --path <大文件路径> --invert-paths
git push origin main --force
```
* --invert-paths 会从历史中移除大文件

* 强制 push 后远程不会再有大文件
### 方法 B：使用 Git LFS
如果你希望保留大文件：
```bash
git lfs install
git lfs track "*.pt" "*.pth"
git add .gitattributes
git add <大文件路径>
git commit -m "Move large files to Git LFS"
git push origin main
```
LFS 会把大文件单独存储，避免超过 100MB 限制
## 4️⃣ 防止以后再误 add/commit 大文件
1. 在仓库根目录创建 .gitignore：
```
# checkpoints / weights
*.ckpt
*.pth
*.pt
*.safetensors
*.onnx

# outputs
outputs/
checkpoints/
runs/
wandb/
logs/

# caches
__pycache__/
*.pyc
.cache/
.ipynb_checkpoints/
.DS_Store
```
2. 安装 Git 钩子（pre-commit hook）限制大文件：
```bash
# 在 .git/hooks/pre-commit 文件里添加
#!/bin/sh
maxsize=100000000  # 100MB
for file in $(git diff --cached --name-only); do
    size=$(stat -c%s "$file")
    if [ "$size" -gt $maxsize ]; then
        echo "Error: $file is larger than 100MB"
        exit 1
    fi
done
```
可阻止 add/commit 超过阈值的大文件
## 💡总结
* 还没 push → git reset / git rm --cached / git commit --amend

* 多条 commit 还没 push → git rebase -i + 移除大文件

* 历史里有大文件 / 已 push → git filter-repo --invert-paths 或 Git LFS

* 预防未来 → .gitignore + pre-commit hook

# git restore和git reset用法区别
```bash
git restore <file>
```
这个指令可以使被改变的文件回退到编辑之前，上一个commit保存的文件，改变的是本地文件。
```bash
git reset HEAD <file>
```
这个指令会撤销暂存区的修改，也就是说不会被add。这条指令相当于
```bash
git restore --staged <file>
```
HEAD就是当前commit以后保存的状态，注意区分工作区/暂存区/HEAD

