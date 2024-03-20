---
author: facsert
pubDatetime: 2024-02-21 20:34:43
title: Python pandas
slug: Python pandas
featured: false
draft: false
tags:
  - Python
  - pandas
description: "Python pandas 模块"
---

## Table of Contents

## 介绍

pandas 是一个用于处理表格型数据的 Python 库, 可以轻松地处理各种结构化数据  

|类型|示例|含义|
|:--:|:--:|:--:|
|Series|s = pd.Series([1, 2, 3])|一维数组|
|DataFrame|df = pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]})|二维数组|

```bash
# 按列创建 pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]})
   A   B     
0  1   4  
1  2   5 
2  3   6

# 按行创建 pd.DataFrame([{'a': 1, 'b': 2}, {'a': 5, 'b': 10, 'c': 20}])
   a   b     c
0  1   2   NaN
1  5  10  20.0
```

## Series

Series 是一维数组, 可以理解为二维数组的一列或 DataFrame 取的一行  

```py
# 创建 Series 数据, 不设置 index, 默认索引从 0 开始
pd.Series(["1st", "2nd", "3rd"])
> 0    1st
> 1    2nd
> 2    3rd

# 创建 Series 数据, 并指定列索引
pd.Series(["1st", "2nd", "3rd"], index=['a', 'b', 'c'])
> a    1st
> b    2nd
> c    3rd

s = pd.Series(["1st", "2nd", "3rd"], index=['a', 'b', 'c'])

# 按列索引取值
s['c'] / s.loc['c'] / s.iloc[2]
> '3rd'

# 按行索引取片段
s[:2] / s[['a', 'b']] / s.loc[['a', 'b']] /s.iloc[range(2)]
> a    1st
> b    2nd

# Series 重新赋值/追加值
s['d'] = '4th'

# Series -> list 转列表
s.tolist()
list(s.array) / list(s.values) / list(s)
> ['1st', '2nd', '3rd']

# Series 数据操作, 字符串叠加, 整数运算
s + s
s * 2
> a    1st1st
> b    2nd2nd
> c    3rd3rd
```

## DataFrame

```py
data = [
  {'name': 'A', 'age': '16', 'height': '1.75'}, 
  {'name': 'B', 'age': '17', 'height': '1.80'}, 
  {'name': 'C', 'age': '18', 'height': '1.85'}, 
  {'name': 'D', 'age': '19', 'height': '1.90'}
]

# index 使用自定义行号
df = pd.DataFrame(data, index=['a', 'b', 'c', 'd'])
>    name age height
> a    A  16   1.75
> b    B  17   1.80
> c    C  18   1.85
> d    D  19   1.90

# DataFrame[column name] -> Serial 获取一列数据
df['name']
> a    A
> b    B
> c    C
> d    D

# 按行名字取行 DataFrame.loc[row name] -> Serial 获取一行数据
# 按行序号去行 DataFrame.iloc[row index] -> Serial 获取一行数据
df.loc['c']
df.iloc[2]
> name         C
> age         18
> height    1.85
> Name: c, dtype: object

# DataFrame.iloc[[row index list]] -> DataFrame 按行序号取多行
df.iloc[range(3)]
>    name age height
> a    A  16   1.75
> b    B  17   1.80
> c    C  18   1.85

# DataFrame 合并
df = pd.concat([df1, df2])

# DataFrame 追加行
df.append(s, ignore_index=True)
```

```py
# 行遍历
for i, r in df.iterrows():
    print(f"{i}: {list(r)}")

> a: ['A', '16', '1.75']
> b: ['B', '17', '1.80']
> c: ['C', '18', '1.85']
> d: ['D', '19', '1.90']

for i, r in df.iterrows():
    print(f"{i}: {dict(r)}")

> a: {'name': 'A', 'age': '16', 'height': '1.75'}
> b: {'name': 'B', 'age': '17', 'height': '1.80'}
> c: {'name': 'C', 'age': '18', 'height': '1.85'}
> d: {'name': 'D', 'age': '19', 'height': '1.90'}

# 修改列
df['name'] = df['name'].apply(lambda x: "pre_" + x)
df['name'] = 'pre' + df['name']
>     name age height
> a  pre_A  16   1.75
> b  pre_B  17   1.80
> c  pre_C  18   1.85
> d  pre_D  19   1.90


# 插入列, 插入序号
df.insert(index, 'name', iterable)
df1.insert(3, "count", [3, 2, 5, 1])
>    name age height  count
> a    A  16   1.75      3
> b    B  17   1.80      2
> c    C  18   1.85      5
> d    D  19   1.90      1

# 删除列, inplace=True 会直接修改原数据
df.drop(columns=['name'], inplace=True)
>     name age height
> a     A  16   1.75
> b     B  17   1.80
> c     C  18   1.85
> d     D  19   1.90
```

```py
# 排序
df.sort_values(by="age")
  name age height
a    A  16   1.75
b    B  17   1.80
c    C  18   1.85
d    D  19   1.90

df = pd.DataFrame({
    'category': ['A', 'A', 'B', 'B', 'C', 'C'],
    'value': [10, 20, 30, 40, 50, 60]
})

  category  value
0        A     10
1        A     20
2        B     30
3        B     40
4        C     50
5        C     60

result = df.groupby('category')['value'].agg(['max', 'min', 'mean'])
          max  min  mean
category
A          20   10  15.0
B          40   30  35.0
C          60   50  55.0

[print(i, dict(r)) for i, r in res.iterrows()]
A {'max': 20.0, 'min': 10.0, 'mean': 15.0}
B {'max': 40.0, 'min': 30.0, 'mean': 35.0}
C {'max': 60.0, 'min': 50.0, 'mean': 55.0}
```

import pandas as pd

# 假设有一个 DataFrame df，其中有两列 'category' 和 'value'
df2 = pd.DataFrame({
    'category': ['A', 'A', 'B', 'B', 'C', 'C'],
    'value': [10, 20, 30, 40, 50, 60]
})

# 按照 'category' 列进行分组，并计算 'value' 列的 最大值、最小值 和 平均值
result = df.groupby('category')['value'].agg(['max', 'min', 'mean'])

# 输出结果
print(result)