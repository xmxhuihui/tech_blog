---
date: '2025-07-21T10:06:59+01:00'
draft: false
title: 'Python Init Use'
---
## __init__.py 是什么？
它是一个 Python 包的标志文件。

没有它，某个目录不会被视为“包”，无法通过 import 方式导入其内部模块（在 Python 3.3+ 中其实不强制了，但很多工程/工具依然依赖它）。
## ✅ __init__.py 里可以写什么？
__init__.py 其实就是一个普通的 Python 脚本，但通常有这几类用途：

### 1. 明确声明这是一个包
```
my_project/
├── __init__.py   # 即使是空的，也表示这是一个包
├── module_a.py
```
### 2. 导出你想暴露的接口（常用）
比如你只想让用户用 from my_project import A 来用你写的类，而不是 from my_project.module_a import A：
```python
# __init__.py
from .module_a import A

__all__ = ['A']
```
### 3. 自动化注册（比如 model registry）
```python
# __init__.py
from .resnet import ResNet
from .vgg import VGG

register_model("resnet", ResNet)
register_model("vgg", VGG)
``` 
### 4. 初始化配置或状态（不常见，但可能有）
例如载入某些全局变量或环境设置：
```python
# __init__.py
import os
MY_GLOBAL_FLAG = os.getenv("MY_FLAG", False)
```
不过这个用法要小心，不要把副作用逻辑放太多进去，否则会造成导入时的隐式执行，调试不方便。
### 5. 子模块结构化（把文件夹当模块用）
如果你有一个子目录结构:
```
my_project/
├── models/
│   ├── __init__.py
│   ├── cnn.py
│   └── rnn.py
```
你可以在 ```models/__init__.py``` 中统一导入：
```python
from .cnn import CNNModel
from .rnn import RNNModel

__all__ = ['CNNModel', 'RNNModel']
```
这样别人就可以：
```python
from my_project.models import CNNModel
```
## 🧱 总结一句话：
__init__.py 就像是包的“门面”或“入口脚本”，你可以选择让它干点活（导入、初始化、注册），也可以什么都不写，只要它存在就行。
