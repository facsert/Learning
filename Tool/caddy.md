---
pubDatetime: 2022-01-19 21:51:23
title: caddy
slug: caddy
featured: false
draft: false
tags:
  - caddy
description: "web 服务器和代理工具 caddy"
---

## 介绍

Caddy 是一款开源的 Web 服务器，它是一个快速、可靠、安全的 HTTP/2 服务器，具有自动 HTTPS、模块化的配置、插件化的架构等特性

[caddy 官方文档](https://caddyserver.com/docs/)

## 安装

[静态二进制文件](https://caddyserver.com/download)

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

## 基本使用

```bash
 # caddy 使用说明
 $ caddy

 # 启动 caddy (阻塞执行, 默认端口 2019)
 $ caddy run

 # 查看 caddy 配置信息 (json 格式, 无配置返回 null)
 $ curl localhost:2019/config/
 > null

 # 后台启动 caddy
 $ caddy start

 # 停止 caddy
 $ caddy stop

 # 重载服务, 进行中的服务请求不会中断
 $ caddy reload
```

## 全局配置

全局配置用于设置一些全局的配置项，比如监听的端口、日志级别、默认网站根目录等  
全局配置必须在文件顶部, 且必须是第一个代码快

```bash
{
    debug
    log {
        # 日志输出(默认 stderr), 可选标准输出: stdout, 忽略: discard, 文件: file /path/to/log.txt
        output file /root/log/caddy.log {
            roll_size 50mb     # 每个日志文件大小 (默认 100MB)
            roll_keep 5        # 保留日志文件个数 (默认 10)
            roll_keep_for 7h   # 保留日志文件时间 (默认 2160h 90天)
            roll_local_time    # 使用本地时间 (默认 UTC 时间)
            roll_uncompressed  # 日志文件是否压缩
        }

        # 日志格式, 可选: console, json, 自定义格式
        format console {
            time_format wall
            level_format upper
            time_local  true
        }

        # 日志级别, 仅有 ERROR 和 INFO 两种日志, 可选: ERROR, INFO
        level  INFO
    }
}
```

## 代理配置

- 服务接口
- 静态文件服务器
- 反向代理配置

```bash
 # Caddyfile 配置 2024 端口, 响应 Hello, Caddy!
 localhost:2024 {
     respond "Hello, Caddy!"
 }

 $ curl localhost:2024
 > Hello, Caddy!

 $ curl localhost:2019/config/
 > {"apps":{"http":{"servers":{"srv0":{"listen":[":2024"],"routes":[{"handle":[{"body":"hello caddy!","handler":"static_response"}]}]}}}}}

 # 指定配置文件(不指定会在当前路径寻找 Caddyfile)
 $ caddy run --config /path/to/Caddyfile
```

```bash
# 静态文件服务器(配置文件路径列表)
:2023 {
    file_server browse
}

# 指定路径为 caddy 文件列表
# http://localhost:2023  => /root/temp
:2023 {
    root * /root/temp
    file_server browse
}
```

```bash
# localhost:2026 代理 8010 8011 端口服务, 并设置轮询
:2026 {
    reverse_proxy {
        to localhost:8010 localhost:8011
        lb_policy random
    }
}

# round_robin 轮询
# random 随机
# least_conn 最少连接数
# ip_hash 根据 IP 哈希
# url_hash 根据 URL 哈希
# first 第一个可用的服务
```

## api 接口

```bash

# 获取服务配置 config 后可以接 json 路径
$ curl localhost:2019/config/ | jq
$ curl localhost:2019/config/apps/http/servers | jq

# 停止服务
$ curl -X POST localhost:2019/stop/

# 获取服务状态
$ curl localhost:2019/status/

# 获取服务统计信息
$ curl localhost:2019/stats/

# 重载服务配置
$ curl -X POST localhost:2019/reload/

# 重新加载 TLS 证书
$ curl -X POST localhost:2019/load_certs/

# 重新加载插件
$ curl -X POST localhost:2019/load_plugins/

# 重新加载模块
$ curl -X POST localhost:2019/load_modules/

# 重新加载主题
$ curl -X POST localhost:2019/load_themes/

# 重新加载所有配置
$ curl -X POST localhost:2019/load_all/
```
