---
date: '2026-03-13T20:45:56Z'
draft: false
title: 'Github Pull and Pip Install Settings in China Region'
---

## github repo pull
### 目前国内较稳定的 GitHub 镜像站（2025–2026 实测）
```bash
git clone https://gitclone.com/github.com/用户/仓库.git
git clone https://hub.fastgit.org/用户/仓库.git
git clone https://github.com.cnpmjs.org/用户/仓库.git
https://bgithub.xyz/用户/仓库
https://kkgithub.com/用户/仓库
https://ghproxy.com/https://github.com/xxx/xxx/releases/download/... (下载 Release 强推)
```
### 通用加速命令：
```bash
git clone https://gh.llkk.cc/https://github.com/microsoft/vscode
```
### 总结：
如果只推荐三个最稳定、最适合 clone 的：

gitclone.com（国内服务器，速度通常最快）
github.com.cnpmjs.org（香港线路，长期可用）
hub.fastgit.org（镜像站中使用人数最多之一）

如需下载 Release：ghproxy.com
## pip install
```bash
pip install xxx -i <清华、阿里镜像站 e.g.https://pypi.tuna.tsinghua.edu.cn/simple>
```
### 设置为默认源
```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```
### 下载torch
国内的话，上交有直接复制了pytorch主站的镜像，但是因为还有一些其他的附加包要安装，所以这里其他的包还是走比如清华或者阿里镜像站。
```bash
pip install torch==2.4.0 torchvision==0.19.0 torchaudio==2.4.0 --index-url https://mirror.sjtu.edu.cn/pytorch-wheels/cu118 --extra-index-url https://pypi.tuna.tsinghua.edu.cn/simple
```
## git下载一个仓库并pip安装
```bash
pip install git+https://gh.llkk.cc/https://github.com/<user>/<repo>.git
pip install git+https://hub.fastgit.org/<user>/<repo>.git
pip install git+https://github.com.cnpmjs.org/<user>/<repo>.git
```
注意：问题在于，这些仓库可能有submodules，但是上面的写法，会导致子模块还是走主站而非镜像站。

### 通用做法：
```bash
vim gitconfig-mirror
export GIT_CONFIG_GLOBAL=./gitconfig-mirror
```
gitconfig-mirror:
```
[url "https://gh.llkk.cc/https://github.com/"]
    insteadOf = https://github.com/
    insteadOf = git@github.com:
    insteadOf = ssh://git@github.com/
```
然后直接```pip install https://github.com/xxx```安装主站仓库，所有的仓库和子模块都会自动跳转。
### Huggingface settings
```bash
https://hf-mirror.com/<repo_id>/resolve/<branch>/<filename>
```
Example:
```bash
wget --content-disposition https://hf-mirror.com/facebook/llama-7b/resolve/main/pytorch_model.bin
```
常用命令：
```bash
# 1. 安装工具
pip install -U huggingface_hub

# 2. 设置镜像环境变量
export HF_ENDPOINT=https://hf-mirror.com
# 或者在python文件头部加上：
import os
os.environ['HF_ENDPOINT'] = 'https://hf-mirror.com'
# 然后使用 huggingface-cli 下载就会自动走镜像，大幅提速

# 3. 下载模型
huggingface-cli download <repo_id> <filename> \
    --local-dir ./weights \
    --local-dir-use-symlinks False
# 举例：
huggingface-cli download \
    meta-llama/Llama-2-7b-hf \
    --local-dir ./llama2 \
    --local-dir-use-symlinks False

```
备注：autodl上的conda是被污染的，经常执行```conda install -c conda-forge```会报解析错误或者卡住，这时候需要自己手动安装一个纯净版的miniconda到workspace/autodl-tmp目录下。
```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh -p /workspace/miniconda3
```
如果是conda安装，先找到配置文件：
```bash
conda config --show-sources
```
通常会看到类似：
```
==> /home/xxx/.condarc <==
```
用vim打开：
```bash
vim ~/.condarc
```
你会看到类似：
```
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
  - defaults
```
直接删除或者添加：
```bash
  - conda-forge
  - defaults
```
注意：顺序很重要，上面的优先级更高。

一般来说重装完miniconda以后可以不添加，直接```pip install -c conda-forge xxx```，明写channel名称。