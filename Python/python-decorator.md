---
pubDatetime: 2026-02-25 17:32:16
title: Python decorator
featured: false
draft: false
tags:
  - Python
description: "Python 装饰器"
---

## 介绍

装饰器是一个接收函数或类并返回函数的特殊**函数**, 在不改变函数代码情况下在函数**执行前**和**执行后**添加操作

## 示例

假设存在一个 wait 函数, 现在需要显示每次该函数的执行花费的时间  
若为 wait 复杂函数, 且需考虑兼容, 保持原有用法, 不能修改代码  

- 新定义一个函数包含原有函数
- 使用装饰器

```py
from time import sleep, time

def wait(n: int) -> None:
    sleep(n)
    return n

def wait_cost(n: int) -> None:
    start = time()
    ret = wait(n)
    print(f"{func.__name__} cost {time() - start}")
    return ret

wait_cost(3)

wait cost 3.0004677772521973
```

需要使用新函数替换旧函数才有新加入的功能

```py
def wait(n: int) -> None:
    sleep(n)
    return n

def func_cost(func):
    def wait_cost(n: int):
        start = time()
        ret = func(n)
        print(f"{func.__name__} cost {time() - start}")
        return ret

    return wait_cost

wait = func_cost(wait)
wait(3)

wait cost 3.0003743171691895
```

创建新函数覆盖原有函数, 在不修改原有代码情况下新增功能

```py
def func_cost(func):
    def wait_cost(n: int):
        start = time()
        ret = func(n)
        print(f"{func.__name__} cost {time() - start}")
        return ret

    return wait_cost

@func_cost
def wait(n: int) -> None:
    sleep(n)
    return n

wait(3)
wait cost 3.0004844665527344
```

使用装饰器, 添加了功能但无需修改原有代码

```py
from time import sleep, time
from functools import wraps

def func_cost(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time()
        ret = func(*args, **kwargs)
        print(f"{func.__name__} cost {time() - start}")
        return ret

    return wrapper

@func_cost
def wait(n: int) -> None:
    sleep(n)
    return n

# 增强通用性, 使用解包适配不同函数参数
# 使用约定俗成名称 wrapper 命名
# 使用 functools.wraps 保留原有函数名称, 注释等内容(否则函数名称变为 wrapper)
```

## 装饰器参数

以上 wait 函数再加一个特性, 执行时间超过阈值才显示, 阈值可以自定义  
在原有装饰器外再嵌套一层即可添加装饰器参数

```py
from time import sleep, time
from functools import wraps

def threshold_cost(threshold: int):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            start = time()
            ret = func(*args, **kwargs)
            t = time() - start
            if t > threshold:
                print(f"{func.__name__} cost {t}")
            return ret

        return wrapper
    return decorator

@threshold_cost(3)
def wait(n: int) -> None:
    sleep(n)

wait(1)
wait(3)

wait cost 3.000560998916626
```

## 标准库装饰器

- dataclass 快速生成类
- contestlib 生成器转为上下文管理器

### dataclasses

`dataclasses.dataclass` 用于快速创建类

```py
# dataclass 根据参数补充对应的方法
def dataclass(
    cls=None, 
    /, 
    *, 
    init=True,  # __init__
    repr=True,  # __repr__
    eq=True,    # __eq__
    order=False, #  __lt__, __le__, __gt__, __ge__
    unsafe_hash=False, # __hash__
    frozen=False, 
    match_args=True,
    kw_only=False, 
    slots=False, 
    weakref_slot=False
):
```

```py
from dataclasses import dataclass

@dataclass
class Student:
    name: str
    age: int

    def hello(self):
        print(f"hello, i am {self.name}")

s = Student("John", 18)
```

### contextlib

`contextlib.contextmanager` 将迭代器转化为 with 的上下文管理器

```py
# with 上下文管理器需要对象实现 __enter__ 和 __exit__ 方法
# 进入 with 代码块执行 __enter__ 方法(无参数)
# 退出 with 代码块执行 __exit__ 方法(异常类型: exc_type, 异常对象: exc_value, 异常堆栈: traceback)
# 若 with 语句抛出异常, 可在 exit 方法处理异常

class Work:

    def __init__(self, name: str):
        self.name: str = name

    def working(self):
        print(f"{self.name} is working")

    def __enter__(self):
        print(f"{self.name} start work")
        return self
    
    def __exit__(self, exc_type, exc_value, traceback):
        print(f"{self.name} stop work")
        print(f"{exc_type=} {exc_value=} {traceback=}")

with Work("John") as w:
    w.working()

John start work
John is working
John stop work
exc_type=None exc_value=None traceback=None
```

```py
from contextlib import contextmanager

class Work:
    
    @classmethod
    def working(cls):
        print(f"{cls.name} is working")

    @contextmanager
    def __new__(cls, name: str):
        try:
            cls.name = name
            print(f"{name} start work")
            yield cls
        except Exception as err:
            print(f"run err: {err}")
        finally:
            print(f"{name} stop work")

with Work("John") as w:
    w.working()

John start work
John is working
John stop work
```

```py
from contextlib import contextmanager

# 将函数转化为上下文结构
@contextmanager
def work():
    try:
        print("init before work")
        yield datetime.now()
    except Exception as err:
        print(f"doing work with err: {err}")
    finally:
        print("clear after work")

with work() as t:
    print(f"start work as {t}")

init before work
start work as 2026-02-25 14:53:07.874587
clear after work
```
