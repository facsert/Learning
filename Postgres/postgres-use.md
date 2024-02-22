---
author: facsert
pubDatetime: 2023-12-06 22:05:18
title: Postgresql configuration
slug: Postgresql configuration
featured: false
draft: false
tags:
  - Postgresql
description: "Postgresql 配置"
---

<!--
 * @Author: facsert
 * @Date: 2023-12-06 22:05:18
 * @LastEditTime: 2023-12-06 22:19:33
 * @LastEditors: facsert
 * @Description:
-->

## Table of Contents

## 安装

```bash
 # 下载 postgres 镜像
 $ docker pull postgres
 $ docker images
 > REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
 > postgres     latest    75b7bff7c3ad   6 weeks ago   425MB
 
 # 使用镜像创建容器, 端口映射, 容器名称, 环境变量(密码), 路径映射
 $ docker run \
     -p 5432:5432 \
     --name postgres \
     -e POSTGRES_PASSWORD=password \
     -v /root/Desktop/Postgres:/var/lib/postgresql/data \
     -d postgres
 
 # 进入容器
 $ docker exec -it postgres bash
 
 # 使用 postgres 用户连接数据库
 $ psql -U postgres
 
 # 查看数据库列表
 \l
                                                      List of databases
   Name    |  Owner   | Encoding | Locale Provider |  Collate   |   Ctype    | ICU Locale | ICU Rules |   Access privileges
-----------+----------+----------+-----------------+------------+------------+------------+-----------+-----------------------
 postgres  | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |
 template0 | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           | =c/postgres          +
           |          |          |                 |            |            |            |           | postgres=CTc/postgres
 template1 | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           | =c/postgres          +
           |          |          |                 |            |            |            |           | postgres=CTc/postgres
(3 rows)
```

PostgreSQL 命令行在处理 SQL 语句时, 不区分大小写, 结尾以分号(;)结尾  
`psql -h host -p port -U username -d databasename -W password`

```sql
\?                                               /* 所有命令帮助 */
\l                                               /* 列出所有数据库 */
\d                                               /* 列出数据库中所有表 */
\dt                                              /* 列出数据库中所有表 */
\d [table_name]                                  /* 显示指定表的结构 */
\di                                              /* 列出数据库中所有 index */
\dv                                              /* 列出数据库中所有 view */
\h                                               /* sql命令帮助 */
\q                                               /* 退出连接 */
\c [database_name]                               /* 切换到指定的数据库 */
\c                                               /* 显示当前数据库名称和用户 */
\conninfo                                        /* 显示客户端的连接信息 */
\du                                              /* 显示所有用户 */
\dn                                              /* 显示数据库中的schema */
\encoding                                        /* 显示字符集 */
select version();                                /* 显示版本信息 */
\i testdb.sql                                    /* 执行sql文件 */
\x                                               /* 扩展展示结果信息，相当于MySQL的\G */
\o /tmp/test.txt                                 /* 将下一条sql执行结果导入文件中 */
```

## 数据库

```sql
/* 创建数据库 */
CREATE DATABASE database_name WITH
  OWNER             = role_name                  /* 数据库所有者 */
  TEMPLATE          = template                   /* 以模板创建(默认为 template1) */
  ENCODING          = encoding                   /* 编码格式(默认 UTF8) */
  LC_COLLATE        = collate                    /* 数据库使用的排序规则 */
  LC_CTYPE          = ctype                      /* 数据库字符分类 */
  TABLESPACE        = tablespace_name            /* 表空间 */
  ALLOW_CONNECTIONS = true | false               /* 是否允许连接 */
  CONNECTION LIMIT  = max_concurrent_connection  /* 最大连接数(默认 -1, 表示无限制) */
  IS_TEMPLATE       = true | false ;             /* 是否为模板数据库 */


/* 创建数据库示例 */
CREATE DATABASE db WITH
  OWNER = postgres
  ENCODING = 'UTF8'
  CONNECTION LIMIT = 100;

```

```sql
/* 创建数据库 */
 CREATE DATABASE person;
 > CREATE DATABASE
 
 \l
                                                  List of databases
 Name   |  Owner   | Encoding | Locale Provider |  Collate   |   Ctype    | ICU Locale | ICU Rules | Access privileges
--------+----------+----------+-----------------+------------+------------+------------+-----------+------------------
 person | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |
 ......
 ......

 /* 连接指定数据库 */
 \c people
 > You are now connected to database "people" as user "postgres".
 
 /* 修改数据库名称(不能修改当前连接的数据库名) */
 ALTER DATABASE person RENAME TO people;
 > ALTER DATABASE
 
 \l
                                                  List of databases
 Name   |  Owner   | Encoding | Locale Provider |  Collate   |   Ctype    | ICU Locale | ICU Rules | Access privileges
 -------+----------+----------+-----------------+------------+------------+------------+-----------+-------------------
 people | postgres | UTF8     | libc            | en_US.utf8 | en_US.utf8 |            |           |


/* 删除指定数据库(不能删除当前连接的数据库名) */
 DROP DATABASE IF EXISTS people;
 > DROP DATABASE
```

## 数据类型

|数据类型|代码|描述|
|:---:|:---|:---|
|布尔型|BOOLEAN|TRUE('true', 't', 'yes', 'y', '1'), FALSE('false', 'f', 'no', 'n', '0') 或 NULL|
|字符类型|CHAR(50)|固定长度的字符类型, 长度不足, 空格补齐, 超出将异常(不推荐)|
|字符类型|VARCHAR(50)|可变长度的字符类型，不补长度, 超出将异常(不带长度与 TEXT 相同)|
|字符类型|TEXT|变长度的字符类型, 存储任意长度的字符串|
|整数类型|SMALLINT/INT2|2字节有符号整数(-32768, 32767)|
|整数类型|INTEGER/INT/INT4|4字节有符号整数(-2147483648, 2147483647)|
|整数类型|BIGINT/INT8|8字节有符号整数(-9223372036854775808, 9223372036854775807)|
|浮点类型|REAL/FLOAT4|单精度浮点数(4字节)|
|浮点类型|DOUBLE PRECISION/FLOAT8|双精度浮点数(8字节)|
|浮点类型|NUMERIC(p, s)|高精度数字, p表示数字总长度, s表示小数位数|
|时间类型|DATE|日期(yyyy-mm-dd)|
|时间类型|TIME|一天中的某个时刻(HH:MM:SS.ssssss)|
|时间类型|TIMESTAMP|时间日期和时刻(YYYY-MM-DD HH:MI:SS[.ssssss])|
|时间类型|INTERVAL|时间间隔|
|时间类型|TIMESTAMP|带时区的时间日期和时刻|
|JSON类型|JSON|JSON格式的字符串|
|数组类型|ARRAY|数组|
|UUID类型|UUID|UUID|

## 创建表

```sql
/* 创建表 */
CREATE TABLE [IF NOT EXISTS] table_name (
   column1 datatype(length) column_contraint,
   column2 datatype(length) column_contraint,
   column3 datatype(length) column_contraint,
   table_constraints
);

/* 创建表示例(create 是关键字不可作为表头) */
CREATE TABLE IF NOT EXISTS students (            /* IF NOT EXISTS 表存在则不创建 */
   id INT NOT NULL PRIMARY KEY,                  /* id 作为主键且不能为空 */
   name VARCHAR(20) NOT NULL,                    /* name 最长为20字符且不能为空 */
   age  INT,                                     /* age 可以为空 */
   locked BOOLEAN NOT NULL DEFAULT false,        /* locked 默认为 false 且不能为空 */
   create_at TIMESTAMP NOT NULL                  /* create_at 时间戳且不能为空 */
);

\d
          List of relations
 Schema |   Name   | Type  |  Owner
--------+----------+-------+----------
 public | students | table | postgres
```

## 表约束

### 主键

使用 `PRIMARY KEY` 为表创建主键

- 一张表只能有一个主键, 表可以没有主键
- 主键必须唯一, 可以由一个列或多个列共同组成
- 主键列不允许为 `NULL`

```sql
/* 创建表, id 为主键 */
CREATE TABLE users (
  id INTEGER PRIMARY KEY,
  name VARCHAR(45),
);

/* 无主键的表, 添加主键 */
ALTER TABLE users ADD PRIMARY KEY (id);

/* 查看表结构 */
\d users
                      Table "public.users"
Column |         Type          | Collation | Nullable | Default
--------+-----------------------+-----------+----------+---------
id     | integer               |           | not null |
name   | character varying(45) |           |          |
Indexes:
    "users_pkey" PRIMARY KEY, btree (id)

/* 删除主键 */
TABLE TABLE users DROP CONSTRAINT users_pkey;
```

设置主键为 SERIAL, UUID 自动生成主键  

```sql
/* SERIAL 列自动生成连续递增整数 */
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(45)
);

/* 使用 UUID 作为主键 */
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(45)
);

/* 多列组合主键 */
CREATE TABLE users (
  id SERIAL,
  name VARCHAR(45),
  PRIMARY KEY (id, name)
);
```

## NOT NULL

使用 `NOT NULL` 使列的值不能为空

```sql
/* id 列不能为空 */
CREATE TABLE users (
    id INT NOT NULL
);

/* 列添加 NOT NULL 约束(需将列所有值设为非空才能添加) */
ALTER TABLE users ALTER COLUMN id SET NOT NULL;

/* 列删除 NOT NULL 约束 */
ALTER TABLE users ALTER COLUMN id DROP NOT NULL;
```

## UNIQUE

使用 `UNIQUE` 使列的值唯一

```sql
/* id 列所有值均唯一 */
CREATE TABLE users (
    id INT UNIQUE
);

/* 添加多列唯一约束 */
CREATE TABLE users (
    id INT,
    name VARCHAR(20),
    CONSTRAINT unique_cols
      UNIQUE (id, name)
);

/* 列添加 UNIQUE 约束(需将列所有值设为非空才能添加) */
ALTER TABLE users ADD CONSTRAINT unique_cols UNIQUE (id);

/* 列删除 UNIQUE 约束 */
ALTER TABLE users DROP CONSTRAINT unique_cols;
```

## CHECK

使用 `CHECK` 约束限制列的值范围, 不符合的数据无法添加  

```sql
/* age 列添加 CHECK 约束 */
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    age INTEGER NOT NULL,
    CONSTAINT user_age 
      CHECK(age > 0)
);

/* 添加 CHECK 约束 */
ALTER TABLE users ADD CONSTRAINT user_age CHECK (age > 0);

/* 删除 CHECK 约束 */
ALTER TABLE users DROP CONSTRAINT user_age;
```
