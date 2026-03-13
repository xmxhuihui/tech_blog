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
### git下载一个仓库并安装
```bash
pip install git+https://gh.llkk.cc/https://github.com/<user>/<repo>.git
pip install git+https://hub.fastgit.org/<user>/<repo>.git
pip install git+https://github.com.cnpmjs.org/<user>/<repo>.git
```

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
