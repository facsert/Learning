---
author: facsert
pubDatetime: 2023-04-07  15:28:43
title: Linux crontab
slug: Linux crontab
featured: false
draft: false
tags:
  - Linux
description: "Linux 定时任务"
---

## Table of Contents

```bash
 # 列出所有定时任务
 $ crontab -l
 > 0 9 * * * echo hello >> /root/temp.log  # test
 
 # 编辑定时任务
 $ crontab -e
```
