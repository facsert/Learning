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
[Gitlab 官方文档](https://docs.gitlab.com/)
[Gitlab 官方中文文档](https://gitlab.cn/docs/)

## 安装

```bash
 # 下载 gitlab 社区版镜像
 $ docker pull gitlab/gitlab-ce:latest
 $ docker images
 > REPOSITORY        TAG     IMAGE ID       CREATED         SIZE
 > gitlab/gitlab-ce  latest  2eac78f2ca26   3 days ago      3.02GB

 # 通过 docker-compose.yml 启动 gitlab
 $ docker compose up -d
 
 # 查看 gitlab 日志
 $ docker logs gitlab
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
    # 设置 gitlab 环境变量和 contain registry
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://192.168.1.100:80'
        gitlab_rails['gitlab_shell_ssh_port'] = 2424

        registry_external_url 'http://192.168.1.100:5005'
        gitlab_rails['registry_enabled'] = true
        gitlab_rails['registry_port'] = "5005"
        gitlab_rails['registry_api_url'] = "http://192.168.1.100:5005"
    # 映射端口
    ports:
      - "80:80"
      - "8443:443"
      - "2424:22"
      - "5005:5005"
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

```bash
# 修改 gitlab 默认语言(root用户无效)
gitlab图标(左上) -> Projects -> Configure Gitlab -> Settings -> Preferences -> Localization -> Default language 设置语言

# 用户管理(root用户创建, 其他用户通过注册申请, root用户通过申请)
gitlab图标(左上) -> Projects -> Configure Gitlab -> Overview -> Users
```

## gitlab runner

gitlab runner 是 gitlab 的一个组件, 用于执行 gitlab 中的任务, 如构建, 测试, 部署等
[gitlab runner 官方文档](https://docs.gitlab.com/runner/)

```bash
 # 下载 gitlab runner 镜像
 $ docker pull gitlab/gitlab-runner:latest
 $ docker images
 > REPOSITORY           TAG      IMAGEID       CREATED        SIZE
 > gitlab/gitlab-runner latest   09c48aa4008e   2 weeks ago    798MB
```

```yml
# 通过 runner 定义 4 种 runner 类型, 用于不同类型项目
services:
  runner-shell:
    image: gitlab/gitlab-runner:latest
    container_name: runner-shell
    restart: always
    volumes:
      - '/home/Gitlab/runner/shell:/etc/gitlab-runner'
      - '/var/run/docker.sock:/var/run/docker.sock'

  runner-python:
    image: gitlab/gitlab-runner:latest
    container_name: runner-python
    restart: always
    volumes:
      - '/home/Gitlab/runner/python:/etc/gitlab-runner'
      - '/var/run/docker.sock:/var/run/docker.sock'

  runner-golang:
    image: gitlab/gitlab-runner:latest
    container_name: runner-golang
    restart: always
    volumes:
      - '/home/Gitlab/runner/golang:/etc/gitlab-runner'
      - '/var/run/docker.sock:/var/run/docker.sock'

  runner-node:
    image: gitlab/gitlab-runner:latest
    container_name: runner-node
    restart: always
    volumes:
      - '/home/Gitlab/runner/node:/etc/gitlab-runner'
      - '/var/run/docker.sock:/var/run/docker.sock'
```

`Admin(左下角) -> CI/CD -> Runners -> Register a new runner`, 填写 runner tag(使用, 分隔), 点击创建 runner  
自动跳转的注册页面(页面带有注册步骤和命令, 复制命令到 gitlab-runner 容器内执行)

```bash
  # runner 配置
  URL: https://your.gitlab.domain
  Token: [Python项目的令牌]
  Description: python-runner
  Tags: python, docker
  Executor: docker
  Default image: python:3.11

 # 进入 gitlab-runner 容器内
 $ docker exec -it gitlab-runner bash
 
 # 使用 gitlab runner 注册页面命令注册 runner
 $ gitlab-runner register  --url http://192.168.31.30  --token glrt-t1_xTWX5umEYfdXNfwxb6n2
  Runtime platform                                    arch=amd64 os=linux pid=30 revision=b92ee590 version=17.4.0
  Running in system-mode.

  # 使用 gitlab 地址
  Enter the GitLab instance URL (for example, https://gitlab.com/):
  [http://192.168.31.30]:
  Verifying runner... is valid                        runner=t1_xTWX5u
  
  # runner 命名
  Enter a name for the runner. This is stored only in the local config.toml file:
  [9bead2d53b48]: runner-node

  # 选择触发器类型
  Enter an executor: shell, ssh, virtualbox, docker-autoscaler, custom, docker, docker-windows, docker+machine, kubernetes, instance, parallels:
  docker

  # 选择 docker 镜像
  Enter the default Docker image (for example, ruby:2.7):
  node:latest
  Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!

  Configuration (with the authentication token) was saved in "/etc/gitlab-runner/config.toml"
```

executor 选择为 docker, 需要使用 docker 镜像, 配置使用本地镜像

```bash
 # runner 容器配置映射到主机路径
 $ vi /home/Gitlab/runner/python/config.toml

[[runners]]
  ... ...
  ... ...
  [runners.docker]
    # 可选值: always(始终拉取最新镜像), if-not-present(本地不存在则拉取), never(始终使用本地镜像)
    pull_policy = "if-not-present"
    ... ...
    ... ...
```

注册机器 shell executor(使用机器的 shell 环境, 可以使用 shell 环境安装的 docker python node 等环境)

```bash
 # ubuntu/debian 添加 apt 源
 $ curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
 
 # 使用安装包安装 gitlab-runner
 $ curl -LJO "https://s3.dualstack.us-east-1.amazonaws.com/gitlab-runner-downloads/latest/deb/gitlab-runner-helper-images.deb"
 $ curl -LJO "https://s3.dualstack.us-east-1.amazonaws.com/gitlab-runner-downloads/latest/deb/gitlab-runner_amd64.deb"

 # 安装 gitlab-runner
 $ sudo apt install gitlab-runner

 # gitlab 新增 runner 复制注册命令到机器上执行
 $ sudo gitlab-runner register  --url http://xxxx --token xxxx

 # gitlab runner 加入 docker 组(CI/CD 无需 root 权限可使用 docker 命令)
 # sudo groupadd docker 创建 docker 组
 $ sudo usermod -aG docker gitlab-runner
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
# 所有 job 默认使用 python:latest 镜像
# 定义 build, test, deploy 三种 job 类型和执行顺序
# test_job1, test_job2 都是 test 类型, 并行执行
# 使用 tags 选择带有 python 标签的 runner 执行

default:
  image: python:latest

stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  tags:
    - python
  script:
    - echo "run build job"

test_job1:
  stage: test
  tags:
    - python
  script:
    - echo "run test job1"

test_job2:
  stage: test
  tags:
    - python
  script:
    - echo "run test job2"

deploy_job:
  stage: deploy
  tags:
    - python
  script:
    - echo "run deploy job"
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

示例
注: 配置中 `CI_IMAGE_NAME` `CI_DEPLOY_PASSWORD` `CI_REGISTRY` `CI_DEPLOY_USER` 等变量需要在 gitlab 项目设置中设置

```yml
default:
  tags:
    - shell

stages:
  - build
  - test
  - deploy

variables:
  TAG: $CI_COMMIT_SHORT_SHA
  DOCKER_BUILDKIT: 1

cache:
  key:
    files:
      - pnpm-lock.yaml
  paths:
    - .pnpm-store/
  policy: pull-push

build_job:
  stage: build
  script:
    - echo "start build $CI_IMAGE_NAME:$TAG"
    - docker build -t $CI_IMAGE_NAME:$TAG .
    - echo $CI_DEPLOY_PASSWORD | docker login $CI_REGISTRY -u $CI_DEPLOY_USER --password-stdin 
    - docker push ${CI_IMAGE_NAME}:$TAG
    - echo "build ${CI_IMAGE_NAME}:${TAG} success"

test_job1:
  stage: test
  script:
    - echo "run test job1"
    - echo "test job1 success"

deploy_job:
  stage: deploy
  script:
    - echo "deploy $CI_IMAGE_NAME:$TAG"
    - echo "TAG=$TAG" > .env
    - echo "IMAGE_NAME=$CI_IMAGE_NAME" >> .env
    - echo $CI_DEPLOY_PASSWORD | docker login $CI_REGISTRY -u $CI_DEPLOY_USER --password-stdin 
    - docker pull ${CI_IMAGE_NAME}:$TAG
    - docker compose up -d
    - docker ps -a | grep $CI_IMAGE_NAME
    - echo "deploy success"
```
