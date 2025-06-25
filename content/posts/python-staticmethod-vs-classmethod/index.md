---
date: '2025-06-21T13:57:53+01:00'
draft: false
title: 'Python Staticmethod & Classmethod'
---
@staticmethod and @classmethod can both be called directly by Class name. But the difference is that @staticmethod does not involve the class itself, while @classmethod involves in class instantiation. Have a look at the example:
```python
class Example:
    version = "1.0"

    @staticmethod
    def add(x, y):
        return x + y

    @classmethod
    def show_version(cls):
        return f"Version: {cls.version}"
```
```python
Example.add(3, 4)          # ✅ 输出: 7
Example.show_version()     # ✅ 输出: "Version: 1.0"

e = Example()
e.add(1, 2)                # ✅ 也能调用，输出: 3
e.show_version()           # ✅ 输出: "Version: 1.0"
```

Another example:
```python
class Animal:
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

class AnimalFactory:
    @classmethod
    def create(cls, kind):
        if kind == "dog":
            return Dog()
        elif kind == "cat":
            return Cat()
        else:
            raise ValueError(f"Unknown kind: {kind}")

```
使用：
```python
animal = AnimalFactory.create("dog")
print(animal.speak())  # 输出: Woof!
```
因此可以看出，@classmethod可以用作类实例化的方法，通过该方法，根据传入不同的参数，可以实现相同基类下不同子类的实例化。

<span style="background-color: yellow; color: black">详情请参考“工厂模式 vs 构造器模式”</span>

## Question: @staticmethod在什么条件下可以替代@classmethod？
@staticmethod 和 @classmethod 虽然都属于类级别的方法，但它们有本质上的区别：

你可以用 @staticmethod 替代 @classmethod，前提是：
你写的方法不依赖类本身（cls）的任何属性或行为。
### ✅ 可以用 @staticmethod 替代 @classmethod 的情况
| 条件                    | 是否可以用 `@staticmethod` |
| --------------------- | --------------------- |
| 方法不需要访问类变量或类方法？       | ✅ 是                   |
| 方法不需要创建类的新实例？         | ✅ 是                   |
| 方法只是一段与类有关但不依赖类状态的逻辑？ | ✅ 是                   |
### ✅ 例子：静态工具函数（不依赖类）
```python
class MathUtils:
    @staticmethod
    def add(a, b):
        return a + b
```
### ❌ 不建议用 @staticmethod 的情况（必须保留 @classmethod）
| 需要功能             | 原因                             |
| ---------------- | ------------------------------ |
| 创建当前类的实例         | 必须用 `cls()`，只能在 `@classmethod` |
| 访问类属性（如默认值、注册表等） | 需要用 `cls.var`                  |
| 可被子类重载并正确返回子类对象  | `@staticmethod` 不支持多态          |
### 例子：from_pretrained() 工厂方法
```python
class Model:
    @classmethod
    def from_config(cls, config):
        return cls(**config)
```
这里必须是 @classmethod，因为你要用 cls() 创建“当前类”的实例，不能用 @staticmethod。
## ✅ 总结：替换建议
| 使用场景              | 用什么装饰器            |
| ----------------- | ----------------- |
| 不依赖类和实例，只是逻辑函数    | `@staticmethod` ✅ |
| 依赖类对象（创建实例或访问类变量） | `@classmethod` ✅  |
| 依赖实例状态            | 普通方法（无装饰器） ✅      |

## 那么什么样的方法建议写成@staticmethod而不是普通method？
这个问题非常关键——很多人滥用普通方法（带 self）导致类职责混乱。

### ✅ 适合写成 @staticmethod 的方法
总结一句话：

当你的方法不需要访问类属性（cls）或实例属性（self）时，强烈建议写成 @staticmethod。

### ✅ 常见的场景和示例
1. 纯工具函数
* 和类逻辑有关，但不需要访问任何实例或类的状态。
```python
class StringUtil:
    @staticmethod
    def snake_case(name):
        return name.replace(" ", "_").lower()
```
2. 通用静态验证逻辑
* 如类型校验、格式校验、路径检查等。
```python
class User:
    @staticmethod
    def is_valid_email(email):
        return "@" in email and "." in email
```
3. 转换函数（数据解析 / 序列化 / 转换等）
* 把一种数据类型转为另一种，不依赖实例。
```python
class Serializer:
    @staticmethod
    def to_json(data):
        import json
        return json.dumps(data)
```
4. 计算类公式 / 公式类方法
* 不依赖类和实例，只是个数学逻辑。
```python
class Physics:
    @staticmethod
    def kinetic_energy(mass, velocity):
        return 0.5 * mass * velocity ** 2
```
5. 通用生成函数，但不关心返回的是哪个类
* 如果你只返回字符串、数值、文件名等固定格式。
```python
class Report:
    @staticmethod
    def generate_filename(prefix):
        import datetime
        return f"{prefix}_{datetime.datetime.now().isoformat()}"
```
### 不适合写成 @staticmethod 的方法（写成普通方法更合理）
| 情形                      | 应该使用             |
| ----------------------- | ---------------- |
| 访问或修改实例属性               | 普通方法（带 `self`）   |
| 与实例生命周期相关的行为（如保存、加载、更新） | 普通方法             |
| 会被继承且希望有不同子类行为          | 用 `self` 或 `cls` |
## ✅ 总结
| 判断条件                   | 推荐写成              |
| ---------------------- | ----------------- |
| 方法不使用 `self` 或 `cls`   | `@staticmethod` ✅ |
| 方法使用 `self`（访问或修改实例属性） | 普通方法 ✅            |
| 方法使用 `cls`（构造类、访问类变量）  | `@classmethod` ✅  |

# 三种方法的使用场景
### ✅ 普通方法（def method(self)）
* 第一个参数是 self，表示实例本身。

* 适用于：需要访问或操作实例属性、实例方法的情况。

例如：

```python
class A:
    def __init__(self, x):
        self.x = x

    def print_x(self):
        print(self.x)  # 操作的是实例属性
```
### ✅ 类方法（@classmethod def method(cls)）
* 第一个参数是 cls，表示类本身。

* 适用于：

    * 访问或修改类属性

    * 工厂方法（根据不同参数返回类的不同实例）

    * 操作和类本身相关但不依赖具体实例的逻辑

* 与“是否是 private 属性”无直接关系。

例如：

``` python
class A:
    _count = 0  # 类属性（可以是private）

    @classmethod
    def increment(cls):
        cls._count += 1
```
### ✅ 静态方法（@staticmethod def method()）
* 没有 self 或 cls 参数。

* 适用于：

    * 与类和实例都无关的辅助方法，只是逻辑上归属于这个类。

    * 比如格式转换函数、校验函数等。

```python
class A:
    @staticmethod
    def add(a, b):
        return a + b
```

## 总结
| 方法类型 | 是否有 `self` | 是否有 `cls` | 主要用途            |
| ---- | ---------- | --------- | --------------- |
| 普通方法 | ✅          | ❌         | 操作实例状态（属性或方法）   |
| 类方法  | ❌          | ✅         | 操作类状态（类属性、构造对象） |
| 静态方法 | ❌          | ❌         | 工具函数、逻辑辅助，无需状态  |
