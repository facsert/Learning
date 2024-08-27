---
author: facsert
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

## 配置

基本使用

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
···

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
# 静态文件服务器(指定配置文件路径列表)
:2023 {
    file_server browse 
}
```

```caddyfile
# 监听 80 端口
http:// {
    # 网站根目录
    root * /var/www/html
    # 网站默认文件
    file_server
}

# 监听 443 端口
https:// {
    # 网站根目录
    root * /var/www/html
    # 网站默认文件
    file_server
    # 自动 HTTPS
    tls {
        dns cloudflare
    }
}
```

## 启动

```bash
sudo systemctl start caddy
```

## 停止

```bash
sudo systemctl stop caddy
```

## 重启

```bash
sudo systemctl restart caddy

