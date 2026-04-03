---
date: '2026-04-03T14:10:19+01:00'
draft: false
title: 'Anaconda Navigator Update in Terminal'
---
@[TOC](使用命令行进行Anaconda Navigator更新)
因为直接在Navigator里面更新，如果在国内的话不翻墙很有可能会失败。即使翻了墙也可能花费很长时间，所以这里直接用命令行，又快又好。
# 更新Anaconda Navigator
```bash
conda update conda
conda update anaconda-navigator
```
~~现在没有[conda update anaconda]这条命令了 别试了~~
如果这两条命令行不通，提示你latest version，但是All required packages are installed，就尝试直接显示命令安装最新版本。
```bash
conda install conda==xx.xx.xx
```
# 更新Jupyter notebook
区分一下更新jupyter库和jupyter notebook
```bash
conda update jupyter
conda update jupyter notebook
```

conda 的更新会比pip慢很多，但是如果已经在anaconda里面安装就不要再用pip安装了，否则可能会损坏原来安装的文件。
==（具体什么不太清楚不过还是尽量不要轻易尝试了）==
其余命令行更新方法请在官方文档中查找：
[https://anaconda.org/anaconda/](https://anaconda.org/anaconda/)

现在从官网下载anaconda的速度也是奇慢无比，国内镜像站的速度还是可观的：
[https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/](https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/)