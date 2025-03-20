---
pubDatetime: 2024-04-07  15:28:43
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
 $ cat /etc/crontab
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
  17 *  *  *  *   root    cd / && run-parts --report /etc/cron.hourly
  25 6  *  *  *   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.daily; }
```

```bash
 # 列出所有定时任务
 $ crontab -l
 # 定时任务 hello 在每日 09:00 执行命令 echo hello >> /root/temp.log
 > 0 9 * * * echo hello >> /root/temp.log  # hello

 $ crontab -l
 # 定时任务 world 在每日 09:00, 09:10, 12:00, 12:10 执行命令 echo world >> /root/temp.log
 > 0,10 9,12 * * * echo world >> /root/temp.log  # world

 # 编辑定时任务
 $ crontab -e
```
