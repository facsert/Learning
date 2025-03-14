---
pubDatetime: 2025-03-04 20:24:37
title: Python f-string
slug: Python f-string
featured: false
draft: false
tags:
  - Python
description: "Python f-string"
---

## 引申

计算机语言通过打印语句输出信息, 通过添加一些修改可以实现多种格式打印

```python
# python 3 种格式化打印方式, 占位符, format, f-string

# 占位符, 类似 C 语言
print("hello %s" % "world")

# format, 使用 {} 占位, 可以使用索引和关键字
print("hello {}".format("world"))
print("hello {a}".format(a="world"))

# f-string, 使用 f 开头, {} 直接使用变量或值
name = "world"
print(f"hello {name}")
print(f"hello {'world'}")
```

## 格式化

除了变量替换外, 还可以对变量进行格式化输出, 如宽度,对齐方向, 填充符号, 小数位数等

```python
# 字符串格式化输出
# {<变量>:<填充符号><对齐方式><宽度>}

# 填充符号, 不填表示空格, 可填任意单个字符
# 对齐方式, < 左对齐(默认), > 右对齐, ^ 居中
# 宽度, 数字表示宽度

s = "play"

# 宽度 10, 左对齐, 空格填充
print(f"[{s:10}]")
> [play      ]

# 宽度 10, 右对齐, 空格填充
print(f"[{s:>10}]")
> [      play]

# 宽度 10, 居中, * 填充
print(f"[{s:*^10}]")
> [***play***]
```

数字格式扩展

```python
# 数字格式化输出
# {<变量>:<填充符号><对齐方式><宽度>.<精度><类型>}

pi = 3.1415925
print(f"[{pi:0<10.3f}]")
> [3.14200000]

# 千分位分隔符
print(f"[{123456:0>10,}]")
> [000123,456]
```
