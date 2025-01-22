---
pubDatetime: 2025-01-13 21:54:43
title: Sqlite3
featured: false
draft: false
tags:
  - Sqlite3
  - Database
description: "Sqlite3 基本配置"
---

## 介绍

Sqlite3 是一款开源的关系型数据库管理系统，它是一个轻量级的离线关系数据库

## 安装

```bash
 # apt 安装 sqlite3
 $ apt install sqlite3 -y

 # python 自带 sqlite3 模块，可以直接用于创建数据库
 $ python -c "import sqlite3; sqlite3.connect('example.db')"
```

## 基本操作

```bash
# 打开数据库文件, 不存在则创建数据库文件
 $ sqlite3 test.db

 # 显示数据库名称和文件
 > .databases
 main: /root/temp/database.db r/w

 # 显示当前数据库中的所有表
 > .tables
 person    nodes

 # 导出数据库到文件
 $ sqlite3 test.db .dump > test.sql

 # 从文件导入数据库
 $ sqlite3 test.db < test.sql

 # 显示所有配置
 > .show

 # 切换显示格式(list | column | html | insert | markdown)
 > .mode markdown
| id |   name    | age |       description        |     created_at      |
|----|-----------|-----|--------------------------|---------------------|
| 1  | Project 1 | 20  | This is a sample project | 2021-01-01 12:00:00 |

 > .mode column
id  name       age  description               created_at
--  ---------  ---  ------------------------  -------------------
1   Project 1  20   This is a sample project  2021-01-01 12:00:00

> .mode insert
INSERT INTO "table"(id,name,age,description,created_at) VALUES(1,'Project 1',20,'This is a sample project','2021-01-01 12:00:00');
```

## 数据操作

```bash

# 创建表
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name TEXT,
    email TEXT
);

# 插入数据
INSERT INTO users (name, email) VALUES ('Alice', 'alice@example.com');

# 查询数据
SELECT * FROM users;

# 更新数据
UPDATE users SET name = 'Bob' WHERE id = 1;

# 删除数据
DELETE FROM users WHERE id = 1;

# 退出数据库
.quit
```
