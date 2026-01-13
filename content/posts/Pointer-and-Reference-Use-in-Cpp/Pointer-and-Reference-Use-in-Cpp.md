---
date: '2025-08-16T17:24:10+08:00'
draft: true
title: 'Pointer and Reference Use in Cpp'
---
```cpp
int* p = &a // 声明指针p，用于储存a的地址，&a是取地址
int& ref = a // 声名引用ref，用作a的别名
*p = 10 //取指针p的原值，并赋值为10
```

### Use when initialization and passing into a function
Pointer: 取一个指针的地址
Reference: 取相同的变量，而不是复制一个新的