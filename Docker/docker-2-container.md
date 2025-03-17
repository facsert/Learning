---
pubDatetime: 2022-10-10 15:28:43
title: Docker Container
slug: Docker Container
featured: false
draft: false
tags:
  - docker
description: "Docker 容器"
---

## Table of Contents

## 容器列表

```bash
 $ docker ps [OPTIONS]                                                         # 显示容器列表及相关信息

 $ docker ps
 $ docker ps -a, --all                                                         # 显示所有容器, 包括未运行的
 $ docker ps -s, --size                                                        # 显示正在运行的容器, 显示容器大小
 $ docker ps -q, --quite                                                       # 仅显示容器 ID

CONTAINER ID   IMAGE         COMMAND                  CREATED        STATUS         PORTS                     NAMES      SIZE
7eab1cd126af   43761bd5b76d  "docker-entrypoint.s…"   43 hours ago   Up 43 hours    27017/tcp                 person     0B (virtual 700MB)
ee0c15a3a0ee   mongo         "docker-entrypoint.s…"   44 hours ago   Up 44 hours    0.0.0.0:8589->27017/tcp   demo       0B (virtual 700MB)
```

| `CONTAINER ID` | `IMAGE`        | `COMMAND`            | `CREATED`    | `STATUS` | `PORTS`            | `NAMES`  | `SIZE`   |
| :------------- | :------------- | :------------------- | :----------- | :------- | :----------------- | :------- | :------- |
| 容器 ID        | 生成容器的镜像 | 启动容器时运行的命令 | 容器创建时间 | 容器状态 | 机器端口->容器端口 | 容器名称 | 容器大小 |

```bash
 $ docker stop    <CONTAINER ID | NAMES>                                       # 关闭容器
 $ docker start   <CONTAINER ID | NAMES>                                       # 启动容器
 $ docker kill    <CONTAINER ID | NAMES>                                       # 杀死容器进程
 $ docker restart <CONTAINER ID | NAMES>                                       # 重启容器
 $ docker rm -f   <CONTAINER ID | NAMES>                                       # 强制删除容器(运行中的容器也会删除)

 $ docker logs    <CONTAINER ID | NAMES>                                       # 显示指定容器 log
 $ docker top     <CONTAINER ID | NAMES>                                       # 列出指定容器进程
 $ docker port    <CONTAINER ID | NAMES>                                       # 显示容器的端口映射

 $ docker exec [OPTIONS] CONTAINER COMMAND [ARG...]                            # 进入容器执行命令(容器在运行状态才可进入)
 $ docker exec -it mongo-slave1 bash                                           # 以交互式进入 mongo-slave1 容器,执行 bash, 并留在容器中(exit 退出容器)
 $ docker exec mongo-slave1 mongosh --version                                  # 进入 mongo-slave1 容器查看 mongosh 版本并退出
 > 1.6.1
```

## 创建容器

使用 `docker run` 命令创建容器, 使用不通参数将容器内端口或路径映射到主机(映射都是 server:docker 结构)

```bash
Usage: docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Options:
  -d, --detach                                                                 # 后台运行容器，并输出容器ID
  -e, --env list                                                               # 设置环境变量，该变量可以在容器内使用
  -h, --hostname string                                                        # 指定容器的hostname
  -i, --interactive                                                            # 以交互模式运行容器，通常与-t同时使用
  -l, --label list                                                             # 给容器添加标签
  --name <name>                                                                # 设置容器名称，否则会自动命名
  --network string                                                             # 将容器加入指定网络
  -p, --publish list                                                           # 设置容器映射端口 <server port>:<docker port>
  -P,--publish-all                                                             # 将容器设置的所有exposed端口进行随机映射
  --restart <restart option>                                                   # 容器重启策略，默认为不重启
    on-failure[:max-retries]：                                                 # 在容器非正常退出时重启，可以设置重启次数。
    unless-stopped：                                                           # 总是重启，除非使用stop停止容器
    always：                                                                   # 总是重启
  --rm                                                                         # 容器退出时则自动删除容器
  -t, --tty                                                                    # 分配一个伪终端
  -u, --user <username>                                                        # 运行用户或者UID
  -v, --volume list                                                            # 容器目录映射到主机目录 <server path>:<docker path>
  -w, --workdir <work path>                                                    # 容器的工作目录
  --privileged                                                                 # 给容器特权
```

容器和主机环境是隔离的, docker 提供一些方法进入容器

- 将容器内端口映射到主机端口, 通过主机端口连接容器
- 将容器内路径映射到主机路径, 通过修改主机映射路径文件同步到容器内
- `docker exec` 命令进入容器

```bash
 # 使用 mongo 镜像创建 demo 容器, 后台运行, 容器 27017 端口映射机器 8589 端口
 $ docker run -d -p 8589:27017 --name demo mongo
 $ docker ps
 CONTAINER ID  IMAGE  COMMAND                 CREATED         STATUS         PORTS                     NAMES
 ee0c15a3a0ee  mongo  "docker-entrypoint.s…"  13 seconds ago  Up 11 seconds  0.0.0.0:8589->27017/tcp   demo

 $ docker run -d \
  -p 5432:5432 \                                                               # 端口映射  server port:docker port
  --name postgres \                                                            # 容器名称
  -e POSTGRES_PASSWORD=root \                                                  # 设置容器内环境变量(数据库密码)
  -e TZ=Asia/Shanghai \                                                        # 设置容器内环境变量(数据库时区)
  -v /root/postgres:/var/lib/postgresql/data \                                 # 容器目录映射到主机路径
  postgres                                                                     # 指定镜像

 # 创建容器(自动命名), 创建后进入容器执行 bash, 退出容器后自动删除容器
 $ docker run -it --rm ubuntu bash
```

## docker-compose

在命令行可以通过 `docker run` 创建容器, 为了更简单更快创建多个复杂容器有了 `docker-compose`  
创建 `docker-compose.yml` 定义容器, 然后一键拉起多个复杂容器, 且可以重复使用  
[Docker Compose 离线安装包](https://github.com/docker/compose/releases)

```bash
 # 下载离线工具后, 改名添加权限, 放到 docker 插件路径下
 $ mv docker-compose-linux-x86_62 docker-compose
 $ chmod +x docker-compose
 $ mkdir -p /usr/lib/docker/cli-plugins
 $ cp docker-compose /usr/lib/docker/cli-plugins/

 $ docker compose version
 > Docker Compose version v2.33.1
```

[docker-compose 配置关键字](https://docs.docker.com/reference/compose-file/)

```yaml
# docker compose v1.27 后 无需 version 关键字
version: "3"

services:
  # service 名称, docker compose 以 service 名称做唯一标识
  mongo:
    image: mongo:latest
    container_name: demo
    ports:
      - 8589:27017

  postgres:
    image: postgres:latest
    container_name: postgres
    restart: always
    ports:
      - 5432:5432
    volumes:
      - /root/postgres:/var/lib/postgresql/data
    environment:
      TZ: Asia/Shanghai
      POSTGRES_PASSWORD: root
```

```bash
 # 在当前路径下寻找 docker-compose.yml 文件创建容器并后台执行
 $ docker compose up -d
 > [+] Running 1/2
 > ⠴ Network docker_default    Created
 > ✔ Container docker-redis-1  Started

 # 指定 docker compose 文件创建容器
 $ docker compose -f docker-compose.yml up -d

 # 默认前台执行, 加 -d 后台. 创建容器服务(容器不存在), 启动服务(容器服务未运行), 重启服务(容器服务运行中)
 $ docker compose up

 # 启动已存在且停止的容器服务(默认后台执行), 添加 service 名称启动指定 service
 $ docker compose start [service]

 # 启动已存在且停止的容器服务(后台执行)
 $ docker compose stop [service]

 # 停止并删除指定容器服务(加 -v 同时删除数据卷)
 $ docker compose down redis
 > [+] Running 2/2
 > ✔ Container docker-redis-1  Removed
 > ✔ Network docker_default    Removed

 # 读取 docker-compose.yml 文件, 查看配置信息
 $ docker compose config
```

```yaml
services:
  backend:
    image: example/database
    environment:
      - TZ=Asia/Shanghai  # 设置时区
    volumes:
      - db-data:/etc/data
    networks:
      - db-network

  backup:
    image: backup-service
    volumes:
      - db-data:/var/lib/backup/data
    networks:
      - db-network

# 设置共享数据卷和网络
volumes:
  db-data:
networks:
  db-network:
```

指定 dockerfile 文件 build 并拉起服务

```bash
 # 配置环境变量
 echo GIT_USERNAME="xxxx" > .env
 echo GIT_PASSWORD="xxxx" >> .env
 echo TAG=$(date +"%Y%m%d") >> .env
```

docker-compose 配置文件

```yml
services:

  backend1:
    build: 
      dockerfile: ./backend/Dockerfile
      context: .
      no_cache: true
      args:
        USERNAME: $GIT_USERNAME
        PASSWORD: $GIT_PASSWORD
    image: backend:${TAG}
    container_name: backend1
    restart: always
    volumes:
      - /home/desktop/log/backend/8091:/root/backend/log
    ports:
      - 8091:8000

  backend2:
    depends_on:
      - backend1
    image: backend:${TAG}
    container_name: backend2
    restart: always
    volumes:
      - /home/desktop/log/backend/8092:/root/backend/log
    ports:
      - 8092:8000
```

```bash
 # build 镜像和拉起容器
 $ docker compose build
 $ docker compose up -d
```
