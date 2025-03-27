---
pubDatetime: 2022-01-19 21:51:23
title: n8n
slug: n8n
featured: false
draft: false
tags:
  - n8n
description: "自动化工具 n8n"
---

## 介绍

n8n 是一款开源的自动化工具，可以帮助你连接各种 API、数据库、文件存储、消息传递系统，并通过流程自动化执行任务

## 安装

```yml
services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: always
    ports:
      - 5678:5678
    volumes:
      - n8n_data:/home/node/.n8n 
    environment:
      TZ: Asia/Shanghai

      # 使用 postgres 数据库
      DB_TYPE: postgresdb 
      DB_POSTGRESDB_DATABASE: n8n
      DB_POSTGRESDB_HOST: 192.168.1.100
      DB_POSTGRESDB_PORT: 5432
      DB_POSTGRESDB_USER: username
      DB_POSTGRESDB_PASSWORD: password
      N8N_PROTOCOL: http           # 告知 n8n 使用 HTTPS
      N8N_SECURE_COOKIE: false     # 关闭 HTTPS 时，关闭安全 cookie(允许外部访问)

volumes:
  n8n_data:
```

```bash
 # 拉起服务
 $ docker-compose up -d

 # 查看日志
 $ docker logs -f n8n
```

## 使用

浏览器打开 `http://localhost:5678` 进入 n8n, 先创建用户名及密码
