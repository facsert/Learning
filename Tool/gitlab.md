---
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

GitLab 是一个开源的的 Git 仓库管理软件, 支持多种版本控制系统  
[Gitlab 官方文档](https://gitlab.cn/docs/)

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
    # 设置主机名(使用本机 IP 避免域名解析问题)
    hostname: "192.168.1.100"
    # 设置 gitlab 环境变量
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://192.168.1.100:80'
        gitlab_rails['gitlab_shell_ssh_port'] = 2424
    # 映射端口
    ports:
      - "80:80"
      - "8443:443"
      - "2424:22"
    # 挂载数据卷
    volumes:
      - "/home/gitlab/etc:/etc/gitlab"
      - "/home/gitlab/log:/var/log/gitlab"
      - "/home/gitlab/opt:/var/opt/gitlab"
    shm_size: "256m"
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

## gitlab CI/CD

```yml
# 全局关键字
workflow: 定义流水线名称
default: 自定义每个阶段的默认值
stages: 自定义阶段名称和顺序
include: 导入外部 yaml 文件配置

# 步骤关键字
after_script: 覆盖作业后执行的一组命令
allow_failure: 允许作业失败。失败的作业不会导致流水线失败
artifacts: 成功时附加到作业的文件和目录列表
before_script: 覆盖在作业之前执行的一组命令
cache: 应在后续运行之间缓存的文件列表
coverage: 给定作业的代码覆盖率设置
dast_configuration: 在作业级别使用来自 DAST 配置文件的配置
dependencies: 通过提供要从中获取产物的作业列表，来限制将哪些产物传递给特定作业
environment: 作业部署到的环境的名称
except: 控制何时不创建作业
extends: 此作业继承自的配置条目
image: 使用 Docker 镜像
inherit: 选择所有作业继承的全局默认值
interruptible: 定义当新运行使作业变得多余时，是否可以取消作业
needs: 在 stage 顺序之前执行的作业
only: 控制何时创建作业
pages: 上传作业的结果，与 GitLab Pages 一起使用
parallel: 应该并行运行多少个作业实例
release: 指示运行器生成 release 对象
resource_group: 限制作业并发
retry: 在失败的情况下可以自动重试作业的时间和次数
rules: 用于评估和确定作业的选定属性以及它是否已创建的条件列表
script: 由 runner 执行的 Shell 脚本
secrets: 作业所需的 CI/CD secret 信息
services: 使用 Docker 服务镜像
stage: 定义作业阶段
tags: 用于选择 runner 的标签列表
timeout: 定义优先于项目范围设置的自定义作业级别超时
trigger: 定义下游流水线触发器
variables: 在作业级别定义作业变量
when: 何时运行作业
```

gitlab-ci.yml 简单示例

```yml
workflow: test

default:
  images: ubuntu:lates

stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "run build job"

test_job1:
  stage: test
  script:
    - echo "run test job1"

test_job2:
  stage: test
  script:
    - echo "run test job2"

deploy_job:
  stage: deploy
  script:
    - echo "run deploy job"
# 所有 job 默认使用 ubuntu 镜像
# 定义 build, test, deploy 三种 job 类型和执行顺序
# test_job1, test_job2 都是 test 类型, 并行执行
```

```yml
# 定义流水线名称
workflow: "gitlab ci/cd"

# 设置每个阶段的默认值(步骤内设置可覆盖全局默认值)
# after_script artifacts before_script cache hooks
# image interruptible retry services tags timeout
default:
  image: node:latest

# 自定义阶段名称和阶段执行顺序(同名阶段并行执行)
# 未定义使用默认值 (build, test, deploy)
stage:
  - step1
  - step2
  - step3
```

## gitlab runner

```bash
 # 下载 gitlab runner 镜像
 $ docker pull gitlab/gitlab-runner:latest
 $ docker images
 > REPOSITORY           TAG      IMAGEID       CREATED        SIZE
 > gitlab/gitlab-runner latest   09c48aa4008e   2 weeks ago    798MB
```

```yml
services:
  gitlab-runner:
    image: gitlab/gitlab-runner:latest
    container_name: gitlab-runner
    restart: always
    volumes:
      - "/home/gitlab/gitlab-runner/config:/etc/gitlab-runner"
      - "/var/run/docker.sock:/var/run/docker.sock"
```

`Admin -> CI/CD -> Runners -> Register a new runner`, 填写 runner tag(runner 名称), 点击创建 runner  
自动跳转的注册页面(页面带有注册步骤和命令, 复制命令到 gitlab-runner 容器内执行)

```bash
 $ docker exec -it gitlab-runner bash

 # 复制注册界面上的命令
 $ gitlab-runner register  --url http://192.168.1.100  --token glrt-GsYgtN8bpu7MpLWzNyap

 $ sudo docker exec -it gitlab-runner bash
root@3339c5f4cca9:/# gitlab-runner register  --url http://192.168.1.100  --token glrt-GsYgtN8bpu7MpLWzNyap
Runtime platform                                    arch=amd64 os=linux pid=32 revision=b92ee590 version=17.4.0
Running in system-mode.
# gitlab 地址
Enter the GitLab instance URL (for example, https://gitlab.com/):
[http://192.168.1.100]:
Verifying runner... is valid                        runner=GsYgtN8bp
# runner命名
Enter a name for the runner. This is stored only in the local config.toml file:
[3339c5f4cca9]: shell
# 选择执行器类型, 这里选择 shell 执行器(使用系统 shell 环境)
Enter an executor: custom, shell, ssh, parallels, virtualbox, instance, docker, docker-windows, docker+machine, kubernetes, docker-autoscaler:
shell
Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!

Configuration (with the authentication token) was saved in "/etc/gitlab-runner/config.toml"
```
