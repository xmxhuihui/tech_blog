---
date: '2025-08-18T18:17:11+08:00'
draft: false
title: 'Specific Terminal Setting in VSCode'
---

### 1️⃣ 打开 VS Code 的终端配置文件

VS Code 的终端可以在 settings.json 里配置多种 shell。

1. 打开 VS Code

2. 打开命令面板：Ctrl+Shift+P → 输入 Preferences: Open Settings (JSON)

3. 找到或添加 "terminal.integrated.profiles.windows"，用来定义新的终端配置。

### 2️⃣ 添加 Anaconda Prompt

Anaconda Prompt 本质上是一个 cmd.exe，启动时会调用 conda 的初始化脚本。假设你的 Anaconda 安装在 C:\Users\YourUser\Anaconda3，可以这样配置：
```bash
"terminal.integrated.profiles.windows": {
    "Anaconda Prompt": {
        "path": ["C:\\Windows\\System32\\cmd.exe"],
        "args": ["/K","C:\\Users\\YourUser\\Anaconda3\\Scripts\\activate.bat"]
    }
}
```

/K 参数会执行完命令后保持终端打开

activate.bat 会激活 base 环境，你也可以指定其他 conda 环境，例如：

```"C:\\Users\\YourUser\\Anaconda3\\Scripts\\activate.bat myenv"```


然后在 VS Code 终端里点击 下拉菜单 → 选择 Anaconda Prompt 就可以了。

### 3️⃣ 添加 Developer PowerShell for VS

Developer PowerShell 是 Visual Studio 自带的，它有一个专用的启动脚本（会设置环境变量、编译工具路径等）。假设你安装的是 VS 2022 Community 版本：
```bash
"terminal.integrated.profiles.windows": {
    "Developer PowerShell": {
        "path": "C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "args": ["-NoExit", "-Command", "& 'C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\Common7\\Tools\\VsDevCmd.ps1'"]
    }
}
```

说明：

    -NoExit 保持终端打开

    VsDevCmd.ps1 会初始化开发者环境

    路径根据你 VS 的安装目录修改

### 4️⃣ 选定默认终端（可选）

如果你希望 VS Code 默认就打开 Anaconda Prompt 或 Developer PowerShell：

```"terminal.integrated.defaultProfile.windows": "Anaconda Prompt"```


或者：

```"terminal.integrated.defaultProfile.windows": "Developer PowerShell"```


## ✅ 总结

1. VS Code 可以配置多个终端，通过 "terminal.integrated.profiles.windows" 添加自定义 shell。

2. Anaconda Prompt：本质上是 cmd.exe + conda 初始化脚本。

3. Developer PowerShell：PowerShell + VsDevCmd.ps1 初始化。

4. 配置好后，可以在 VS Code 终端下拉菜单中直接切换。