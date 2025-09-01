---
pubDatetime: 2024-02-22 20:31:18
title: Postgresql database
slug: Postgresql database
featured: false
draft: false
tags:
  - Postgresql
description: "Postgresql 数据库"
---

## Table of Contents

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

## 备份

使用 pg_dump 备份数据库

```bash
 # 导出单个数据库所有数据及表的逻辑关系约束
 $ pg_dump -U $pg_username -h $pg_host -p $pg_port $pg_db_name -Fc -v -f $output
 # 导入
 $ pg_restore -U $pg_username -h $pg_host -p $pg_port $pg_db_name -v $dump

 # 导出
 $ pg_dump -U postgres -h localhost -p 5432 base -Fc -v -f /var/lib/postgresql/data/backup.dump

pg_dump: last built-in OID is 16383
pg_dump: reading extensions
pg_dump: identifying extension members
pg_dump: reading schemas
pg_dump: reading user-defined tables
pg_dump: reading user-defined functions
pg_dump: reading user-defined types
pg_dump: reading procedural languages
pg_dump: reading user-defined aggregate functions
pg_dump: reading user-defined operators
pg_dump: reading user-defined access methods
pg_dump: reading user-defined operator classes
pg_dump: reading user-defined operator families
pg_dump: reading user-defined text search parsers
pg_dump: reading user-defined text search templates
pg_dump: reading user-defined text search dictionaries
pg_dump: reading user-defined text search configurations
pg_dump: reading user-defined foreign-data wrappers
pg_dump: reading user-defined foreign servers
pg_dump: reading default privileges
pg_dump: reading user-defined collations
pg_dump: reading user-defined conversions
pg_dump: reading type casts
pg_dump: reading transforms
pg_dump: reading table inheritance information
pg_dump: reading event triggers
pg_dump: finding extension tables
pg_dump: finding inheritance relationships
pg_dump: reading column info for interesting tables
pg_dump: finding table default expressions
pg_dump: flagging inherited columns in subtables
pg_dump: reading partitioning data
pg_dump: reading indexes
pg_dump: flagging indexes in partitioned tables
pg_dump: reading extended statistics
pg_dump: reading constraints
pg_dump: reading triggers
pg_dump: reading rewrite rules
pg_dump: reading policies
pg_dump: reading row-level security policies
pg_dump: reading publications
pg_dump: reading publication membership of tables
pg_dump: reading publication membership of schemas
pg_dump: reading subscriptions
pg_dump: reading large objects
pg_dump: reading dependency data
pg_dump: saving encoding = UTF8
pg_dump: saving standard_conforming_strings = on
pg_dump: saving search_path = 
pg_dump: saving database definition
pg_dump: dumping contents of table "public.alert"
pg_dump: dumping contents of table "public.boards"
pg_dump: dumping contents of table "public.upgrade_history"
pg_dump: dumping contents of table "public.version_history"

 # 导入
 $ pg_restore -U postgres -h localhost -p 5432 -d postgres -v /database_backup.dump
pg_restore: connecting to database for restore
pg_restore: creating TABLE "public.alert"
pg_restore: creating SEQUENCE "public.alert_id_seq"
pg_restore: creating SEQUENCE OWNED BY "public.alert_id_seq"
pg_restore: creating TABLE "public.boards"
pg_restore: creating SEQUENCE "public.boards_id_seq"
pg_restore: creating SEQUENCE OWNED BY "public.boards_id_seq"
pg_restore: creating SEQUENCE "public.sys_upgrade_id_seq"
pg_restore: creating SEQUENCE OWNED BY "public.sys_upgrade_id_seq"
pg_restore: creating TABLE "public.upgrade_history"
pg_restore: creating SEQUENCE "public.upgrade_history_id_seq"
pg_restore: creating SEQUENCE OWNED BY "public.upgrade_history_id_seq"
pg_restore: creating TABLE "public.version_history"
pg_restore: creating SEQUENCE "public.version_history_id_seq"
pg_restore: creating SEQUENCE OWNED BY "public.version_history_id_seq"
pg_restore: creating DEFAULT "public.alert id"
pg_restore: creating DEFAULT "public.boards id"
pg_restore: creating DEFAULT "public.upgrade_history id"
pg_restore: creating DEFAULT "public.version_history id"
pg_restore: processing data for table "public.alert"
pg_restore: processing data for table "public.boards"
pg_restore: processing data for table "public.upgrade_history"
pg_restore: processing data for table "public.version_history"
pg_restore: executing SEQUENCE SET alert_id_seq
pg_restore: executing SEQUENCE SET api_cases_id_seq
pg_restore: executing SEQUENCE SET boards_id_seq
pg_restore: executing SEQUENCE SET code_coverage_records_id_seq
pg_restore: executing SEQUENCE SET daily_cases_id_seq
pg_restore: executing SEQUENCE SET frequency_io_id_seq
pg_restore: executing SEQUENCE SET stable_attrs_daily_id_seq
pg_restore: executing SEQUENCE SET sys_upgrade_id_seq
pg_restore: executing SEQUENCE SET upgrade_history_id_seq
pg_restore: executing SEQUENCE SET version_history_id_seq
pg_restore: creating CONSTRAINT "public.alert alert_pkey"
pg_restore: creating CONSTRAINT "public.boards boards_number_key"
pg_restore: creating CONSTRAINT "public.boards boards_pkey"
pg_restore: creating CONSTRAINT "public.upgrade_history upgrade_history_pkey"
pg_restore: creating CONSTRAINT "public.version_history version_history_pkey"
```
