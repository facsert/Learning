---
author: facsert
pubDatetime: 2024-03-12 17:32:16
title: Python datetime
slug: Python datetime
featured: false
draft: false
tags:
  - Python
description: "Python 版本控制工具 datetime"
---

## Table of Contents

## 介绍

datetime 是 python 操作日期和时间的标准库  

## datetime

## timedelta

timedelta 表示一段持续的时间, 可用于创建时间片段和日期做加减运算  
`class timedelta(days=0, seconds=0, microseconds=0, milliseconds=0, minutes=0, hours=0, weeks=0)`  

```py
from datetime import timedelta

one_hour = timedelta(hours=1)

```
