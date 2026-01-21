---
pubDatetime: 2026-01-21 21:12:43
title: NodeJS Date
featured: false
draft: false
tags:
  - Node
description: "Node 标准库 Date"
---

## 介绍

Date 对象是 Node 标准库的世间库, 以国际时间 UTC 1970-01-01T00:00:00 为零点

- GMT 格林威治标准时间
- UTC GMT+0 时区
- CST UTC+8 东八区

## 创建时间

```js
// 返回当前时间点的 Date 对象(Z 表示 UTC 时间, UTC=CST-08:00)
$ new Date()
2026-01-21T13:17:19.258Z

 // 返回时间字符串
$ Date()
'Wed Jan 21 2026 21:16:32 GMT+0800 (中国标准时间)'

$ new Date().toString()
'Wed Jan 21 2026 22:06:56 GMT+0800 (中国标准时间)'

new Date().toLocaleString()
'2026/1/21 22:07:24'

new Date().toUTCString()
'Wed, 21 Jan 2026 14:08:06 GMT'

ew Date().toISOString()
'2026-01-21T14:43:40.321Z'

// 返回距离起始点的毫秒数
$ Date.now()
1769001633310

$ new Date().getTime()
1769004314024

// 使用毫秒数创建时间对象
$ new Date(1769001633310)
2026-01-21T13:20:33.310Z

// 创建时间戳对象, 按参数创建本地时区时间戳并转为 UTC 时区时间
// 以整数为月份参数, 月份从 0 开始
$ new Date(2026, 0, 1, 0, 0, 0)
2025-12-31T16:00:00.000Z

$ new Date("2026-01-01T00:00:00")
2025-12-31T16:00:00.000Z

// 创建 UTC 时间, 指定时区
$ new Date(Date.UTC(2026, 0, 1, 0, 0, 0))
2026-01-01T00:00:00.000Z

$ new Date("2026-01-01T00:00:00Z")
2026-01-01T00:00:00.000Z
```

## 时间解析

`Date.parse` 解析字符串返回距离起始点毫秒数  
字符串解析默认值 `2001-01-01T00:00:00`  

|字符串|时间对象|
|:-|:-|
|`2025Z`|2025-01-01T00:00:00Z|
|`03-02`|2001-03-01T16:00:00.000Z|
|`03Z`|2001-03-01T00:00:00.000Z|
|`03 08:10:05Z`|2001-03-01T08:10:05.000Z|
|`2025-02-03 08:10:05Z`|2025-02-03T08:10:05.000Z|
|`2025-02-03 08:10:05+08:00`|2025-02-03T00:10:05.000Z|

```js
// 解析字符串, 解析失败返回 NaN
// 2025-01-01T00:00:00
// 2025-01-01 00:00:00
// 2025/01/01 00:00:00
$ Date.parse('2025-01-01T00:00:00')
1735660800000

$ t = new Date()                       // 2026/1/21 22:46:32 CST

$ t.toString()                         // Wed Jan 21 2026 22:46:32 GMT+0800 (中国标准时间)
$ t.toUTCString()                      // Wed, 21 Jan 2026 14:46:32 GMT
$ t.toISOString()                      // 2026-01-21T14:46:32.250Z
$ t.toJSON()                           // 2026-01-21T14:46:32.250Z
$ t.toDateString()                     // Wed Jan 21 2026
$ t.toTimeString()                     // 22:46:32 GMT+0800 (中国标准时间)
$ t.toLocaleString('en-US')            // 1/21/2026, 10:46:32 PM
$ t.toLocaleString('zh-CN')            // 2026/1/21 22:46:32
$ t.toLocaleDateString('en-US')        // 1/21/2026
$ t.toLocaleDateString('zh-CN')        // 2026/1/21
$ t.toLocaleTimeString('en-US')        // 10:46:32 PM
$ t.toLocaleTimeString('zh-CN')        // 22:46:32


$ t.getTime()                          // 1769006792250
$ t.getFullYear()                      // 2026 年份, 返回年份
$ t.getMonth()                         // 0 月份, 从 0 开始(0-11)
$ t.getDate()                          // 21 日期, 1 月 21 号
$ t.getDay()                           // 3 周x, 1/21 为周三, 周日为 0
$ t.getHours()                         // 22 小时, 
$ t.getMinutes()                       // 46 分钟
$ t.getSeconds()                       // 32 秒

$ t.setFullYear(2025)                  // 设置年份 
$ t.setMonth(0)                        // 一月份, 从 0 开始(0-11)
$ t.setDate(1)                         // 1 日期, 1-31
$ t.setHours(0)                        // 0 小时, 0-23
$ t.setMinutes()                       // 0 分钟, 0-59
$ t.setSeconds()                       // 0 秒, 0-59
```

## 时间操作

Node 标准库没有类似`timedelta` 或 `duration` 时间间隔对象和 Date 对象运算  
Date 运算需要转为整数运算

```js
// Date
$ t1 = new Date("2025-01-01 00:00:00Z")
$ t2 = new Date("2025-01-02 00:00:00Z")

$ t2 > t1                              // true
$ t1 === t2                            // false
$ t2.getTime() === t3.getTime()        // true

$ t2 - t1                              // 时间差 86400000ms=>86400s=>24h
$ t1 + t2                              // 字符串拼接, 无效运算

$ new Date(t1.getTime()+24*60*60*1000) // 时间转整数运算后转为时间对象
```
