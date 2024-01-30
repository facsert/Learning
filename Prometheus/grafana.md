---
author: facsert
pubDatetime: 2024-01-30 21:12:43
title: Grafana
slug: Grafana
featured: false
draft: false
tags:
  - Prometheus
  - Grafana
description: "Grafana 是一个数据可视化工具"
---

Grafana 是一个强大的可视化平台, 可以集成多种数据工具进行可视化  

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

默认端口是 3000
初始用户 admin  
初始密码 admin
