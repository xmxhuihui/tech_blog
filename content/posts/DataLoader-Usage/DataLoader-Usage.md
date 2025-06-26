---
date: '2025-06-26T12:01:19+01:00'
draft: false
title: 'DataLoader Usage'
---
DataLoader 有几个非常重要的好处，尤其是在训练大型模型或大数据集时:
### 🔧 1. 自动批处理（Batching）
你可以设置 batch_size=32 或其他值，DataLoader 会自动帮你把数据分成一批一批的张量。如果不用 DataLoader，你得自己写代码切割数据。

### 🚀 2. 多线程预加载数据（并行加载）
DataLoader 的 num_workers 参数可以开启多个进程并行读取和预处理数据，提高训练效率。如果你是直接传入数据，就无法做到并行加载。

### 🧹 3. 自动打乱数据（Shuffling）
设置 shuffle=True 后，DataLoader 每个 epoch 会自动打乱数据，防止模型记住数据顺序导致过拟合。

### 🧪 4. 支持自定义数据处理（Dataset）
你可以继承 torch.utils.data.Dataset 定义复杂的加载逻辑，比如从磁盘加载图片、读 CSV、处理 NLP 数据等，DataLoader 能自动和这些数据类配合使用。

### 🧼 5. 节省内存
如果数据太大不能一次性加载进内存，DataLoader 支持 懒加载 ——每次只加载一个 batch，而不是把整个数据集加载进来。

### ✅ 举个例子
你当然可以这么做：
```python
inputs = torch.randn(1000, 3, 32, 32)
labels = torch.randint(0, 10, (1000,))
outputs = model(inputs)  # 直接喂给模型
```
但更实际的训练方式是：
```python
from torch.utils.data import DataLoader, TensorDataset

dataset = TensorDataset(inputs, labels)
loader = DataLoader(dataset, batch_size=64, shuffle=True)

for batch_inputs, batch_labels in loader:
    outputs = model(batch_inputs)
    loss = loss_fn(outputs, batch_labels)
    loss.backward()
    optimizer.step()
```
这更可控、更高效、更适用于大规模训练。
你可以不用 DataLoader，但一旦数据稍复杂或规模稍大，它就几乎是必不可少的工具了。

DataLoader加载的dataset不必须是 torch.utils.data.Dataset 的子类，但 实质上需要实现与 Dataset 类似的接口，否则 DataLoader 没法正确读取数据。
### ✅ DataLoader 能接受的对象要求：
只要传入的对象实现了这两个方法：

__getitem__(self, index)：返回索引 index 对应的数据项。

__len__(self)：返回数据集的大小。

就可以被 DataLoader 使用。

这其实是所谓的 “duck typing” —— 你不需要从 Dataset 继承，只要“看起来像”就行了。

### ✅ 举个例子：不用继承 Dataset 的自定义类
```python
class MyData:
    def __init__(self):
        self.data = torch.randn(100, 10)
        self.labels = torch.randint(0, 2, (100,))
    
    def __getitem__(self, index):
        return self.data[index], self.labels[index]

    def __len__(self):
        return len(self.data)

my_dataset = MyData()
loader = torch.utils.data.DataLoader(my_dataset, batch_size=16, shuffle=True)

for x, y in loader:
    print(x.shape, y.shape)
```
你可以看到虽然 MyData 不是 Dataset 子类，但只要实现了 __getitem__ 和 __len__，就能被 DataLoader 使用。

### ✅ 那为什么还要用 Dataset 基类？
继承 Dataset 有这些好处：

可读性更好，明确告诉别人这是个 PyTorch 数据集。

IDE 提示友好、文档规范。

有些工具（如 torchvision、lightning）会检查是不是 Dataset 子类。

### 总结：
不是必须继承 Dataset，但你传给 DataLoader 的对象必须像个 Dataset ——也就是实现 __getitem__ 和 __len__。

## 对比一下 __getitem__ 和 __getattr__ 的区别：
### 1. __getitem__: 用于方括号 obj[key] 访问
```python
class MyDataset:
    def __getitem__(self, index):
        return f"Item {index}"

ds = MyDataset()
print(ds[0])  # 输出：Item 0
```
* 被 obj[0], obj["key"], obj[slice] 这种下标方式调用。

* 常用于容器类（如列表、字典、Dataset 等）。

* 返回的是某个“元素”。

### 2. __getattr__: 用于点号 obj.attr 访问 不存在的属性
```python
class MyObject:
    def __getattr__(self, name):
        return f"No attribute named '{name}'"

obj = MyObject()
print(obj.foo)  # 输出：No attribute named 'foo'
```
* 只有在属性 不存在 的时候才被调用！

* 不会拦截已经定义好的属性（__dict__ 或 __init__ 设置过的）。

* 主要用于动态属性访问，比如构建懒加载属性、访问外部资源等。
### 🚫 注意：它们不会互相替代
```python
class Test:
    def __getattr__(self, name):
        return 42
    def __getitem__(self, index):
        return 99

t = Test()
print(t["abc"])  # 输出：99，用 __getitem__
print(t.abc)     # 输出：42，用 __getattr__
```
### 对比表：
| 特性      | `__getitem__` | `__getattr__`             |
| ------- | ------------- | ------------------------- |
| 调用方式    | `obj[key]`    | `obj.attr`（仅当 `attr` 不存在） |
| 常见用途    | 容器、数据集、张量、索引  | 动态属性访问、懒加载                |
| 触发条件    | 总是触发          | 只有当属性不存在时才触发              |
| 不能拦截的情况 | `obj.attr`    | 已存在的属性（如 `__init__` 里设的）  |
| 示例      | `dataset[5]`  | `model.encoder`           |

