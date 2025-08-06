---
pubDatetime: 2025-07-31 09:58:53
title: Database too many client already
featured: false
draft: false
tags:
  - Database
description: "数据库连接超过上限"
---

## 介绍

偶然一次拉起后端项目, 执行报错 `connection failed: connection to server at "xx.xx.xx.xx", port 5432 failed: FATAL:  sorry, too many clients already`, 由报错可知数据库已到达连接上限，无法再申请连接  
使用 Postgres 数据库, Postgres 默认最大连接数为 100

## 分析

有两种方法解决

- 增加数据库连接上限
- 减少不必要的项目连接数据库

我回想了之前一些连接数据库的项目和脚本有三类

- Grafana 看板有多个图表显示数据库数据
- FastAPI 后端项目, 使用封装 psycopg_pool 的 Database 类, 默认最大 10 个连接
- 定时任务脚本, 使用封装 psycopg_pool 的 Database 类, 默认最大 10 个连接

## 验证

```sql
-- 网上找到查询连接的 sql, 
SELECT * FROM pg_stat_activity;
```

根据 sql 执行结果, 发现有 82 个连接是同一个 IP, 其余 IP 只有零星 1 ~ 2 条, 数据库和多个任务都部署在该 IP 机台  
有两个连接带有 sql 语句确认是 Grafana 上设置的 sql, 查看 Grafana 数据源设置, `Connecttion limit` 设置默认值 100, 修改为 10, 再次查看连接数没有减少  
分析 Database 类, 将 max_size 从 10 减小到 5, 重启部分后端或直接关闭, 连接数仍然没有减少  

```py
class Database:
    """ database module """
    is_init = False
    
    @classmethod
    def init(cls) -> None:
        """ 数据库初始化连接 """
        if cls.is_init:
            return
        
        for conn in DATABASE:
            if getattr(cls, conn.dbname, None) is not None:
                continue

            setattr(cls, conn.dbname, ConnectionPool(
                min_size=1,
                max_size=5,
                conninfo=" ".join(f"{k}={v}" for k, v in asdict(conn).items())
            ))

        cls.is_init = True
        atexit.register(cls.close)

    @contextmanager
    def __new__(cls, dbname: DBName="postgres"):
        cls.init()
        pool: ConnectionPool = getattr(cls, dbname, None)
        conn = None
        try:
            with pool.connection() as conn:
                with conn.cursor(row_factory=dict_row) as cursor:
                    yield cursor
        except Exception as err:
            logger.error(f"database execute error: {err}")

    @classmethod
    def close(cls):
        for conn in DATABASE:
            pool = getattr(cls, conn.dbname, None)
            pool and pool.close()
```

思考: Database 类使用 with 语句, 会自动关闭连接, 且通过 atexit 注册了关闭连接, 关闭项目连接也会关闭, 是否还有其它连接？  
在机台查找所有 python 项目逐个排查  

```bash
# 发现大量定时任务脚本进程(crontab 定时任务脚本在固定时间执行后会自动退出)
ps -ef |grep python
root     3642348 3642346  0 Apr09 ?        00:00:00 /bin/sh -c /root/miniforge3/envs/fastapi/bin/python /root/desktop/schedule/dump.py # dump
root     3741118 3741116  0 Jun29 ?        00:00:00 /bin/sh -c /root/miniforge3/envs/fastapi/bin/python /root/desktop/schedule/dump.py # dump
root     3741806 3741803  0 Jun29 ?        00:00:00 /bin/sh -c /root/miniforge3/envs/fastapi/bin/python /root/desktop/schedule/version_history.py # version
root     3847231 3847228  0 Jul13 ?        00:00:00 /bin/sh -c /root/miniforge3/envs/fastapi/bin/python /root/desktop/schedule/version_history.py # version
root     3906963 3906959  0 Jun10 ?        00:00:00 /bin/sh -c /root/miniforge3/envs/fastapi/bin/python /root/desktop/schedule/version_history.py # version
root     4042444 4042441  0 May21 ?        00:00:00 /bin/sh -c /root/miniforge3/envs/fastapi/bin/python /root/desktop/schedule/version_history.py # version
```

强制关闭定时任务进程后, 冗余连接果然减少, 可以确定就是定时任务未正常退出导致连接未正常断开  
重复测试发现, 当定时脚本使用了多线程执行, 当某个线程执行 raise 错误就会卡死该线程一直不退出, 脚本卡死, 连接无法释放
