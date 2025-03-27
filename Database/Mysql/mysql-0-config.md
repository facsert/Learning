---
pubDatetime: 2023-09-12 20:17:32
title: Mysql Configuration
slug: Mysql Configuration
featured: false
draft: false
tags:
  - Mysql
description: "Mysql 基本配置"
---

## Table of Contents

通过 docker 拉取 mysql 镜像  
根据镜像生成容器, 配置文件和端口映射, 设置 root 密码  
进入容器, 使用 root 用户打开 mysql 命令行

```bash
 $ docker pull mysql
 $ docker images
 > mysql latest 99afc808f15b 4 weeks ago 577MB

 $ docker run -d -v /root/Desktop/Mysql:/root -p 3306:3306 --name sql -e MYSQL_ROOT_PASSWORD=111111 mysql
 $ docker exec -it sql bash

```

docker-compose.yml 配置文件

```yml
# 使用 docker-compose.yml 拉起
services:
  mysql:
    image: mysql:latest # 或 mariadb:latest
    environment:
      MYSQL_DATABASE: default_db # 默认数据库名称
      MYSQL_USER: user # 可选：默认用户
      MYSQL_PASSWORD: password # 可选：用户密码
      MYSQL_ROOT_PASSWORD: root_password # Root 密码（必须设置）
    volumes:
      - root/mysql:/var/lib/mysql
    ports:
      - "3306:3306"
```

```bash
> mysql -u root -p
> SHOW DATABASES;
mysql> show DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

> CREATE DATABASE sql;
Query OK, 1 row affected (0.01 sec)

> USE sql;
Database changed
```
