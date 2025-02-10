---
pubDatetime: 2022-01-19 21:51:23
title: rabbitmq
slug: rabbitmq
featured: false
draft: false
tags:
  - rabbitmq
description: "消息队列工具 rabbitmq"
---

## 介绍

RabbitMQ 是一个开源的、跨平台的、可扩展的、支持多种语言的消息队列

## 安装

```bash
 $ docker pull rabbitmq:management
 $ docker run -itd --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.13-management
 
 # 启动成功后，访问 http://localhost:15672 即可进入管理界面
```
