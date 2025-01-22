---
pubDatetime: 2024-03-05 14:18:43
title: Redis Configuration
slug: Redis Configuration
featured: false
draft: false
tags:
  - Redis
description: "Redis 基本配置"
---

## Table of Contents

## 安装

docker 安装 docker-stack(自带 redisinsight), 便于 web 界面修改和查看数据

```bash
 $ docker pull redis/redis-stack

 $ docker run -d --name redis-stack -v /root/Desktop/redis/data:/data -p 6379:6379 -p 8001:8001 redis/redis-stack:latest

 $ docker ps
 > CONTAINER ID IMAGE                    COMMAND          CREATED      STATUS      PORTS                                                                                 NAMES
 > 0f52fb6bad6e redis/redis-stack:latest "/entrypoint.sh" 3 hours ago  Up 3 hours  0.0.0.0:6379->6379/tcp, :::6379->6379/tcp, 0.0.0.0:8001->8001/tcp, :::8001->8001/tcp  redis-stack

 # --name: 容器名
 # -v: linux 下路径映射容器内 /data (/data 是容器存放数据位置)
 # -p: 端口映射, 6379 是 redis 端口, 8001 是 redisinsight web 端口
```

```yml
# 使用 docker-compose.yml 拉起
services:
  redis-stack:
    image: redis/redis-stack:latest
    container_name: redis-stack
    ports:
      - "6379:6379"
      - "8001:8001"
    volumes:
      - /root/redis/data:/data
    restart: always
```

浏览器打开 `http://localhost:8001` 进入 redisinsight 界面

## 数据持久化

`redis` 数据是保存内存中, 一旦关机，内存数据会丢失, redis 支持将数据保存为本地文件

- 手动保存
- 自动保存策略

```bash
# redis-cli (redis-stack 支持命令, 不区分大小写)

# 主动保存数据库内容
> bgsave
"Background saving started"

# 上一次保存数据的时间点
> lastsave
(integer) 1723623129

# 保存策略 (300 100: 300s 内超过 100次修改触发, 每两个数据 1 个策略)
> config get save
1) "SAVE"
2) "3600 1 300 100 60 10000"
```

## 连接

```py
# pip install redis
import redis

pool = redis.ConnectionPool(host='localhost', port=6379, decode_responses=True)
r = redis.Redis(connection_pool=pool)

print(r.get("host"))
print(r.hgetall("node"))
```

## 数据类型

Redis 命令不区分大小写, 键值对是大小写敏感

### String

Redis 数据库中, 字符串数据就是简单的键值对, key-value

```bash
> SET test "hello redis"
"OK"

> GET test
"hello redis"
```

### Hash

Hash 是多个键值对的集合`{k1-v1, k2-v2}`

```bash
# 创建 students 字段, 添加键值对
> HSET students "John" 18
(integer) 1

# students 添加键值对
> HSET students "Lily" 18
(integer) 1

# 获取键值对的值
> HGET students John
"18"

# 获取所有键值对
> HGETALL students
1) "John"
2) "18"
3) "Lily"
4) "18"
```

### List

### Set

### Json

### Stream
