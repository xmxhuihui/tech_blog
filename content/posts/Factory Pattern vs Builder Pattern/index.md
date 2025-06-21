---
date: '2025-06-21T14:09:55+01:00'
draft: false
title: 'Factory Pattern vs Builder Pattern'
---
# 工厂模式（Factory Pattern）
目的：
* 负责根据条件决定实例化哪一个具体类。

* 隐藏具体类的创建细节，客户端只和工厂接口打交道。

适用场景：
* 需要根据不同参数创建不同类型的对象。

* 需要解耦调用者和具体类的依赖。

* 常见于插件架构、模型加载、根据配置文件选择实现等。

特点：
* 返回的是具体类的实例。

* 创建逻辑一般比较简单，直接调用构造函数。

举例：
```python
class ShapeFactory:
    @staticmethod
    def create_shape(shape_type):
        if shape_type == 'circle':
            return Circle()
        elif shape_type == 'square':
            return Square()
```
# 构造器模式（Builder Pattern）
目的：
* 负责分步骤构造复杂对象，使得构建过程和表示分离。

* 允许同样的构建过程产生不同的表示（对象）。

适用场景：
* 需要创建的对象比较复杂，构建过程复杂且多步骤。

* 需要按不同方式构造复杂对象的不同部分。

* 希望构建过程灵活且易于扩展。

特点：
* 通常有一个“导演（Director）”控制构建步骤。

* 通过一步步调用 builder 的方法完成复杂对象的组装。

* 关注“如何构造”，而不仅是“造哪个”。

举例：
```python
class CarBuilder:
    def __init__(self):
        self.car = Car()

    def build_engine(self):
        self.car.engine = Engine()

    def build_wheels(self):
        self.car.wheels = [Wheel() for _ in range(4)]

    def get_result(self):
        return self.car

# 导演调用
builder = CarBuilder()
builder.build_engine()
builder.build_wheels()
car = builder.get_result()
```

# 工厂模式的不同实现方式
在 Python 中实现“工厂模式”（Factory Pattern）通常用于创建对象时隐藏构造细节，让调用方只需关心“我要什么”，而不是“怎么造”。这在深度学习、插件系统、配置加载等场景中非常常见。

### ✅ 工厂模式核心思想
将类的创建逻辑封装在一个函数或类方法中，以便根据输入参数动态返回某种类型的对象。
## 🧩 常见实现方式
### ✅ 方式一：用 @classmethod 实现工厂方法（最常见）
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
### ✅ 方式二：字典映射法（简洁优雅）
```python
animal_map = {
    "dog": Dog,
    "cat": Cat,
}

def create_animal(kind):
    try:
        return animal_map[kind]()
    except KeyError:
        raise ValueError(f"Unknown kind: {kind}")
```
### ✅ 方式三：注册机制（适合扩展型场景，如注册模型）
```python
class ModelFactory:
    _registry = {}

    @classmethod
    def register(cls, name):
        def decorator(model_cls):
            cls._registry[name] = model_cls
            return model_cls
        return decorator

    @classmethod
    def create(cls, name, *args, **kwargs):
        if name not in cls._registry:
            raise ValueError(f"Model {name} not found")
        return cls._registry[name](*args, **kwargs)

# 注册模型
@ModelFactory.register("mlp")
class MLPModel:
    def __init__(self, input_dim):
        self.input_dim = input_dim

@ModelFactory.register("cnn")
class CNNModel:
    def __init__(self, channels):
        self.channels = channels
```
使用：
```python
model = ModelFactory.create("mlp", input_dim=128)
```
