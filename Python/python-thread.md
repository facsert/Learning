---
pubDatetime: 2024-09-24 08:52:15
title: Python thread
slug: Python thread
featured: false
draft: false
tags:
  - Python
description: "Python 多线程"
---

## 介绍

单线程表示程序中只有一个执行流, 程序任一时间只执行一个任务  
多线程表示有多个执行流, 单个程序在同一时间执行多个任务

多线程本质是 CPU 多核心, 如 CPU 8 核 12 线程, 表示 8 个物理核心, 4 个核心支持超线程, 1 个核心支持双线程  
其余 4 个核心对应 1 个核心, 若硬件仅有 1 个核心且无超线程, 代码层面使用多线程结果依然是单线程(线程切换)

## 多线程

`python` 语言内置 GIL 锁(互斥锁), 同一时间只有 1 个线程能执行, 实际是在多个线程间来回来回切换  
GIL 锁使得多线程在执行 CPU 计算密集任务不能节省时间, 多线程执行 IO 密集任务可以节约时间

```py
import timeit
from threading import Thread


def compute():
    for _ in range(100_000_000):
        pass

def single_compute():
    compute()
    compute()

def multi_compute():
    t1, t2 = Thread(target=compute), Thread(target=compute)
    t1.start()
    t2.start()

    t1.join()
    t2.join()

print(f"single: {timeit.timeit('single_compute()', number=1, globals=globals()) = }")
print(f"multi: {timeit.timeit('multi_compute()', number=1, globals=globals()) = }")

# 计算密集任务使用多线程结果与单线程结果相差不大, 类似抄两篇文章, 不论来回切还是按顺序都需要一直抄
single: timeit.timeit('single_compute()', number=1, globals=globals()) = 3.9793272567912936
multi: timeit.timeit('multi_compute()', number=1, globals=globals()) = 3.7624008180573583
```

```py
import timeit
from threading import Thread
from time import sleep

def io():
    sleep(2)

def single_io():
    io()
    io()

def multi_io():
    t1, t2 = Thread(target=io), Thread(target=io)
    t1.start()
    t2.start()

    t1.join()
    t2.join()

print(f"single: {timeit.timeit('single_io()', number=1, globals=globals()) = }")
print(f"multi: {timeit.timeit('multi_io()', number=1, globals=globals()) = }")

# io 密集任务近乎真实多线程(瓶颈主要在 IO, CPU 大部分时间在等待)
# 实际 io 任务受线程切换开销, 磁盘 IO 等因素, 多线程 IO 任务不一定会比单线程快
single: timeit.timeit('single_io()', number=1, globals=globals()) = 4.000157259404659
multi: timeit.timeit('multi_io()', number=1, globals=globals()) = 2.000672407448292
```

python 多线程更大的意义在于创建多个工作流, "同时" 执行多个任务

```py
from threading import Thread

def test():
    print("do some task")

# 创建线程
t = Thread(target=test)

# 线程后台执行
t.start()

# 等待线程执行完成
t.join()
```

```py
from time import sleep, time
from datetime import datetime
from threading import Thread

from loguru import logger

class Chan:
    """ 单个线程封装
    Params:
        func: callable 可执行函数
        params: dict|None=None 函数参数
        count: int=1 设定函数执行次数(小于 0 表示无限次数)
        timeout: int=0 设定超时时间(小于等于 0 表示无限时间)
        interval: int=0 循环执行间隔
    """

    def __init__(self,
        func: callable,
        params: dict|None=None,
        count: int=1,
        timeout: int=0,
        interval: int=0,
    ) -> None:
        self.timeout: int|float = timeout if timeout > 0 else float('inf')
        self.count: int|float = int(count) if count > 0 else float('inf')
        self.interval: int = interval

        self.func: callable = func
        self.params: dict = params if params else {}

        self._running: bool = True

        self.output: dict[datetime, any] = {}
        self.thread: Thread = Thread(target=self.function, daemon=True)
        self.pid: int = self.thread.native_id
        self.alive: bool = self.thread.is_alive()

    def function(self) -> None:
        """ 线程执行函数 """
        if self._running is False:
            raise RuntimeError("Thread has already been started")

        index, timeout = 0, time() + self.timeout
        while True:
            if not self._running:
                break

            self.output.update({datetime.now(): self.func(**(self.params))})
            sleep(self.interval)
            index += 1

            if (index >= self.count or time() > timeout):
                self._running = False
                break

        self.alive = False

    def run(self) -> int:
        """ 执行线程 """
        self.thread.start()
        self.pid = self.thread.native_id
        self.alive = self.thread.is_alive()
        return self.pid

    def wait(self, timeout=None) -> dict[datetime, any]:
        """ 等待线程结束 """
        self.thread.join(timeout)
        self.alive = self.thread.is_alive()
        return self.output

    def stop(self) -> bool:
        """ 主动停止线程(当次循环执行完后停止) """
        self._running = False
        self.alive = self.thread.is_alive()
        return self.alive is False

def pool(chans: list[Chan], size: int=5, interval: float=0.1) -> list[Chan]:
    """ 线程池执行池
        chans list[Chan]: 线程列表
        size int: 线程池最大数量
        interval float: 轮询间隔时间
    """
    if not isinstance(size, int) or size <= 0:
        raise ValueError("size must be int and bigger than 0")

    group: list[Chan] = []
    pool: list[Chan] = []
    chan_list = chan_list[::-1]

    while len(pool) < size and chan_list:
        chan = chan_list.pop()
        chan.run()
        pool.append(chan)

    while pool:
        for index, chan in enumerate(pool):
            if chan.alive:
                continue

            group.append(pool.pop(index))
            if chan_list:
                ch = chan_list.pop()
                ch.run()
                pool.append(ch)

        sleep(interval)

    return group

if __name__ == '__main__':
    def wait(second: int):
        logger.info(f"wait {second} second")
        sleep(second)
        return second

    c1 = Chan(func=wait, params={'second': 3}, count=2, timeout=6, interval=1)
    c2 = Chan(func=wait, params={'second': 2}, count=2, interval=1)

    pool([c1, c2], 2, 0.1)
    logger.info(f"{c1.output=}, {c1.alive=}, {c1.pid=}")
    logger.info(f"{c2.output=}, {c2.alive=}, {c2.pid=}")
    logger.info("finish")
```
