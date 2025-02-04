---
pubDatetime: 2024-01-30 21:12:43
title: Grafana
slug: Grafana
featured: false
draft: false
tags:
  - Prometheus
description: "Grafana 是一个数据可视化工具"
---

## Table of Contents

## 安装

Grafana 用于展示 Prometheus 采集的监控数据, 通过 promQL 语句绘制图表或使用第三方模板进行数据可视化  
[Grafana Download](https://grafana.com/grafana/download?pg=graf&plcmt=deploy-box-1)
[Grafana Template](https://grafana.com/grafana/dashboards/)

```bash
 $ tar -zxvf grafana-enterprise-9.5.9.linux-arm64.tar.gz
 $ cd grafana-9.5.9 && ./bin/grafana-server
 > ...
 > logger=licensing t=2023-12-07T15:06:25.331289546+08:00 level=info msg="Validated license token" appURL=http://localhost:3000/ source=disk status=NotFound
 > ...
```

配置文件 `conf/default.ini`, 示例 `conf/sample.ini`  
修改 `default_language` 选项为 `detect`(使用浏览器语言)

默认端口是 3000  
初始用户 admin  
初始密码 admin

## 界面

<!-- ![侧边栏](./assets/siderbar.png) -->

- 仪表盘: 监控面板列表
- 连接: 连接第三方数据源泉(prometheus 或 数据库)
- 管理: grafana 设置和用户管理

## 数据源

Grafana 本身只用于显示数据无法采集数据, 需要其它工具提供数据  
Grafana 使用 promql, sql 从数据源中获取数据, 在 web 上使用图表, 表格等方式显示

常用的数据源(Grafana 自带):

- prometheus
- alertmanager
- mysql
- postgres

部分数据源 grafana 如 mongo 有支持, 但需要下载额外插件

## 仪表盘

有两种方式创建面板

- json 文件导入
- 自定义

### json 导入

- 进入面板页面, 点击右上角 **分享**, 选择 **导出**, **保存至文件**
- 官方提供的模板 [Grafana Template](https://grafana.com/grafana/dashboards/) 下载(部分使用旧版本生成, 可能不兼容或者需要修改)
- 进入仪表盘页面, 点击 **新建**, 选择 **导入**, 选择面板 json 文件导入

## 自定义

创建一个面板, 在面板上自定义多个图表, 每个图表自定义数据源, 图表类型, 显示样式  
[官方创建面板教程](https://grafana.com/docs/grafana/latest/dashboards/)  
[官方创建图表教程](https://grafana.com/docs/grafana/latest/panels-visualizations/)

1. 进入仪表盘页面, 点击 **新建**, 选择 **新建仪表板**, 选择面板 **添加可视化**
2. 选择数据源, 配置仪表盘, 右上角 **apply** 完成单个图表
3. 右上角 **保存图标**, 填写 commit 保存面板

### 配置

面板配置: 面板名称, 时间等基本设置, 面板全局变量, JSON 配置

<!-- ![设置](./assets/setting.png) -->

图表配置: 图表类型, 数据内容, 显示样式

<!-- ![图表](./assets/table.png) -->

### 全局变量

面板全局变量使用

<!-- ![变量](./assets/varibale.png) -->

设置全局变量

<!-- ![变量配置](./assets/var_tag.png) -->

```bash
# 获取 node_uname_info 命令包含 tag 属性的列表
label_values(node_uname_info,tag)

# 获取 node_uname_info 命令包含 host 属性且 tag 值等于全局变量中的 tag 的数据列表
label_values(node_uname_info{tag="$tag"},host)
```

```bash
# promql 中使用全局变量
node_time_seconds{host="$host",tag="$tag"} - node_boot_time_seconds{host="$host",tag="$tag"}

# 特殊变量
$__timeFrom Grafana 选择的时间起始值  $__timeFrom -> timestamp
$__timeTo Grafana 选择的时间结束值  $__timeTo -> timestamp
$__timeFilter 判断时间点是否在 Grafana 时间段内容 $__timeFilter(timestamp) -> bool
```

## 脚本

使用脚本快速操作 `grafana` 服务

```bash
CURRENT_DIR=$(pwd)
SCRIPT_DIR="$(cd "$(dirname "$0")" && pwd)"
START_COMMAND="./bin/grafana-server"

function kill_process() {
    cd $SCRIPT_DIR
    if [[ ! -f pid ]]; then
        echo -e "\033[31m pid not exists \033[0m"
        return 1
    fi
    local pid=$(cat pid)
    if [[ -z $pid ]]; then
        echo -e "\033[31m pid is empty \033[0m"
        return 1
    fi

    kill -15 $pid >/dev/null 2>&1
    rm pid
    return 0
}

function start_process() {
    cd $SCRIPT_DIR
    nohup $START_COMMAND >/dev/null 2>&1 &
    local pid=$!
    ps -aux | grep -v grep | grep $pid >/dev/null 2>&1
    echo $pid > pid
    return 0
}

function check_process() {
    cd $SCRIPT_DIR
    if [[ ! -f pid ]]; then
        echo -e "\033[31m pid not exists \033[0m"
        return 1
    fi
    local pid=$(cat pid)
    if [[ -z $pid ]]; then
        echo -e "\033[31m pid is empty \033[0m"
        return 1
    fi

    ps -aux | grep -v grep | grep $pid
    if [[ $? -ne 0 ]]; then
        echo -e "\033[31mProcess is not running\033[0m"
        return 1
    fi
    echo -e "\033[32mProcess is running\033[0m"
    return 0
}

usage=$(cat <<EOF
  -h/--help      show help   \n
  --restart      restart service    \n
  --kill         close service    \n
  --check        check service \n
EOF
)

declare -a params
while [[ $# -gt 0 ]]; do
    case $1 in
      -h|--help)
        echo -e $usage
        exit 0
        ;;
      --restart)
        cd $SCRIPT_DIR
        kill_process
        start_process
        check_process
        cd $CURRENT_DIR
        shift
        ;;
      --kill)
        cd $SCRIPT_DIR
        kill_process
        check_process
        cd $CURRENT_DIR
        shift
        ;;
      --check)
        cd $SCRIPT_DIR
        check_process
        cd $CURRENT_DIR
        shift
        ;;
      *)
        echo "param $1"
        params+=($1)
        shift
        ;;
    esac
done
```
