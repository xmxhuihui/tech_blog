---
date: '2025-06-20T22:19:11+01:00'
draft: false
title: 'Python Decorator'
---
 基本模式：
 ```python
 def decorator(func):
 	def wrapper(*args, **kwargs):
 		<Preprocessing>
 		result = func(*args, **kwargs)
 		<Postprocessing using the 'result'>
 		return xxx
 @decorator
 def function (*args, **kwargs):
 	xxx
 	return xxx

if __name__=="__main__":
	further_result = function(a, b, c, {e: f, g: h})
 ```
 修饰器的意思其实就是字面意思，原始函数进行初步处理传出一个结果到修饰器中，修饰器进行进一步处理（修饰），最后返回的是修饰器进一步处理后的结果。

不仅仅只有函数可以构建装饰器，类也可以用于构建装饰器，在构建装饰器类时，需要将原本装饰器函数的部分实现于__call__函数中即可：
```python
from functools import wraps
class a_decorator(object):
    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):
        @wraps(self.func)
        def wrapTheFunction():
            print('Do some work before calling!')
            return self.func(*args, **kwargs)
        return wrapTheFunction()

@a_decorator 
def math(*args, **kwargs):
    return 'I love math!'

m = math()
print(m())
# output:
# Do some work before calling!
# I love math!
```