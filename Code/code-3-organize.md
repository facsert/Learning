---
pubDatetime: 2022-10-21 21:20:45
title: 组织代码
slug: 组织代码
featured: false
draft: false
tags:
  - code
description: "组织代码功能"
---

## Table of Contents

- 模块化
- 分离工具代码
- 少写代码

## 模块化

分离相关性不大的代码, 一个模块做一件事  
模块化使得代码易于测试和扩展

## 少写代码

不要重复造轮子, 复用已有实现

```python
def unique_list(array):
    pass

def sort_list_one():
    pass

def sort_list_two():
    pass

unique_list = list(set(raw_list))                # 使用集合的特性消除重复数据(集合每个元素唯一)
sort_list = sorted(raw_list)                     # 使用库函数排序
```

## 分离工具代码

抽离与系统无关的代码

```python
def compare_list_average(list1, list2):
    # 计算 list1 的平均值
    sum1, length1 = 0, len(list1)
    for i in list1:
        sum1 += i
    average1 = sum1 / length1

    # 计算 list2 的平均值
    sum2, length2 = 0, len(list2)
    for i in list2:
        sum2 += i
    average2 = sum2 / length2

    return average1 > average2


def average(array):
    array_sum, array_length = 0, len(array)
    for i in array:
        array_sum += i
    return array_sum / array_length

def compare_list_average(list1, list2):
    average1 = average(list1)
    average2 = average(list2)
    return average1 > average2
```
