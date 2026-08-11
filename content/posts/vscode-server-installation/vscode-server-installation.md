---
date: '2026-08-11T17:46:06+08:00'
draft: false
title: 'Vscode Server Installation'
---
# 服务器安装vscode server方法
## 安装 code-server（推荐）
### 1. 一键安装

Ubuntu/Debian/CentOS 等大部分发行版：

```bash
curl -fsSL https://code-server.dev/install.sh | sh
```

安装脚本会自动识别系统并安装对应软件包。

### 2. 启动服务
```bash
sudo systemctl enable --now code-server@$USER
```
这样会开机自动启动。

### 3. 查看配置
```bash
vim ~/.config/code-server/config.yaml
```

默认配置类似：

```yaml
bind-addr: 0.0.0.0:8080
auth: password
password: your-password
cert: false
```

密码就在这里。

### 4. 浏览器访问

本机测试：

```
http://127.0.0.1:8080
```
```bash
sudo systemctl restart code-server@$USER
```

即可通过：

```
http://服务器IP:8080
```
访问。