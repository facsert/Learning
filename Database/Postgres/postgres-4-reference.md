---
pubDatetime: 2024-08-01 20:03:38
title: Postgresql reference
slug: Postgresql reference
featured: false
draft: false
tags:
  - Postgresql
description: "Postgresql 参考"
---

## Table of Contents

## function

- postgres 字符串函数

```sql

-- length 获取字符串长度 length(s text)

-- repeat 重复字符串 repeat(s text, n int)
-- replace 替换字符串 replace(s text, sub text, new text)
-- reverse 反转字符串 reverse(s text)

-- trim 去除字符串前后的空格 trim(s text)
-- trim(BOTH '*' FROM s) 去除字符串 s 左右两边的 * 符号

-- lower 字符串转小写 lower(s text)
-- upper 字符串转大写 upper(s text)

-- concat 字符串拼接 concat(s1 text, s2 text)
-- position 获取字符串的位置 position(sub text in s text)

postgres=# SELECT length('abc') AS "LENGTH";
 LENGTH
--------
      3
(1 row)
```

- postgres 数值函数

```sql
-- abs 获取绝对值 abs(n numeric) -> numeric
-- random 获取随机数 random() -> numeric[0,1) m n 之间取值 random() * (n - m) + m
-- round 浮点保留小数位, 四舍五入 round(n numeric, d int) -> numeric  round(2.35, 1) -> 2.4
```

- postgres 时间函数

```sql
-- now 获取当前时间 now() -> timestamp  "2024-08-01 20:03:38.000000"
-- current_date 获取当前日期 current_date -> date  "2024-08-01"
-- current_time 获取当前时间 current_time -> time  "20:03:38.000000"
-- current_timestamp 获取当前时间 current_timestamp -> timestamp  "2024-08-01 20:03:38.000000"
-- DATE 获取日期函数 DATE(timestamp) -> date  DATE('2024-08-01 20:03:38.000000') -> "2024-08-01"

-- pg_sleep 等待 n 秒 pg_sleep(seconds numeric)
```

- postgres 聚合函数

```sql
-- count 统计行的数量 count(column)
-- sum 列求和 sum(column)
-- avg 列平均值 avg(column)
-- max 列最大值 max(column)
-- min 列最小值 min(column)
```
