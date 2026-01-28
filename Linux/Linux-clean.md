---
pubDatetime: 2024-09-23  14:20:43
title: Linux clean
featured: false
draft: false
tags:
  - Linux
description: "Linux 磁盘清理"
---

## 介绍

Linux 机台不划分盘, 大多数软件工具下载内容或缓存都存在用户根目录, 导致系统所在盘空间急速消耗

```bash
# 查询硬盘占用
$ df -h

# 查询指定路径下文件/文件夹空间占用
$ du -h --max-depth=1 /root | sort -h
```

占用空间较大内容

- journal 日志
- apt 缓存
- docker
- nodejs
- 工具缓存 ~/.cacche

## 清理

```bash
# 查看 journal 日志占用
$ journalctl -x --disk-usage
Archived and active journals take up 4.0G in the file system.

# 清理日志, 仅保存 100M
$ journalctl --vacuum-size=100M

# 配置 /etc/systemd/journald.conf 限制日志大小
[Journal]
SystemMaxUse=10M

# 清理 apt-get 缓存
$ apt-get clean

# docker 占用查询
$ docker system df

# 清理 build cache
$ docker system prune --volumes

# 清除 docker 镜像
$ docker rmi xxx

# npm 缓存清空
$ npm cache clean --force

# 清除 pip 缓存
$ pip cache purge
```

## 移动

系统有多块盘时, 可以将大文件转移到其它盘挂载位置  
如挂载盘到 `/home/dev`, 配置工具路径到该路径下或设置软链接

```bash
# 配置 /etc/systemd/system/docker.service docker 根目录到指定位置
# 设置 --data-root 到 /home/dev/docker
# docker 默认路径是 /var/lib/docker
[Service]
ExecStart=/usr/bin/dockerd --data-root=/home/dev/docker

# 查询 docker 根目录
$ docker info | grep -i "Docker Root Dir"

# ~/.cache 是多个工具存放缓存的路径, 可以直接删除
$ rm -rf ~/.cache

# 在挂载盘下创建路径建立软链接, 可以无需改配置
$ mv ~/.cache /home/dev/cache
$ ln -s /home/dev/cache ~/.cache
```
