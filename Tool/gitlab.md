---
author: facsert
pubDatetime: 2022-01-19 21:51:23
title: gitlab
slug: gitlab
featured: false
draft: false
tags:
  - gitlab
description: "代码仓库 gitlab"
---

## 介绍

GitLab是一个开源的的Git仓库管理软件, 支持多种版本控制系统  

## 安装

```bash
 # 下载 gitlab 社区版镜像
 $ docker pull gitlab/gitlab-ce:latest
 $ docker images
 > REPOSITORY        TAG     IMAGE ID       CREATED         SIZE
 > gitlab/gitlab-ce  latest  2eac78f2ca26   3 days ago      3.02GB
 
 # 通过 docker-compose.yml 启动 gitlab
 $ docker compose up -d
```

```yml
# docker-compose.yml 配置 gitlab
services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab
    restart: always
    hostname: 'codehub.com'
    # 设置 gitlab 环境变量
    environment: 
      GITLAB_OMNIBUS_CONFIG: | 
        external_url 'http://codehub.com'
        gitlab_rails['gitlab_shell_ssh_port'] = 2424
    # 映射端口
    ports:
      - '80:80'
      - '8443:443'
      - '2424:22'
    # 挂载数据卷
    volumes:
      - '/home/gitlab/etc:/etc/gitlab'
      - '/home/gitlab/log:/var/log/gitlab'
      - '/home/gitlab/opt:/var/opt/gitlab'
    shm_size: '256m'
```

浏览器打开 `http://localhost:80` 打开 gitlab 登录页面  
默认用户名: root  
默认密码: 随机生成的密码(进入容器查看), 登录后修改密码  

```bash
 # 进入容器内查询密码
 $ docker exec -it gitlab bash
 $ cat /etc/gitlab/initial_root_password |grep -i password:
 > Password: NJ5g67y9/N1OL9Pod8FpDT4plkYdoMylfzw7vLz3/hM=

 # 容器 /etc 路径已映射到宿主机 /home/gitlab/etc
 $ cat Gitlab/etc/initial_root_password| grep -i password:
 > Password: NJ5g67y9/N1OL9Pod8FpDT4plkYdoMylfzw7vLz3/hM=
```

注: 登录后点击头像(顶部) -> Preference -> Password 修改密码

## 配置

```bash
# 修改 gitlab 默认语言(root用户无效)
gitlab图标(左上) -> Projects -> Configure Gitlab -> Settings -> Preferences -> Localization -> Default language 设置语言  

# 用户管理(root用户创建, 其他用户通过注册申请, root用户通过申请)
gitlab图标(左上) -> Projects -> Configure Gitlab -> Overview -> Users
```