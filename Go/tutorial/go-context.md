---
pubDatetime: 2024-08-12 11:24:43
title: Go Context
slug: Go Context
featured: false
draft: false
tags:
  - Go
description: "Go 上下文"
---

`context.Context` 用于在多个协程中设置截止时间, 同步信号, 传递值

```go

type Context interface {
    // 截至时间
    Deadline() (deadline time.Time, ok bool)
    // context 被取消后关闭
    Done() <-chan struct{}
    // 结束原因, 超时(DeadlineExceeded)/取消(Canceled)
    Err() error
    // 存取键值对
    Value(key interface{}) interface{}
}
```

```go
var wg sync.WaitGroup

// 创建上下文和取消函数, 设置 3s 超时时间
ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
defer cancel()

work := func(ctx context.Context, t time.Duration) {
    wait := 1 * time.Second
    LOOP:
        for {
            select {
            case <- ctx.Done():
                fmt.Printf("work done %v\n", ctx.Err())
                break LOOP
            default:
                if (wait > t) {
                    fmt.Printf("timeout with %v %v\n", t, ctx.Err())
                    break LOOP
                }
                wait += 1 * time.Second
                time.Sleep(1*time.Second)
            }
        }
    wg.Done()
}

wg.Add(2)
go work(ctx, 4*time.Second)
go work(ctx, 2*time.Second)

// 模拟主线程操作
time.Sleep(3*time.Second)

// 主动取消
cancel()
wg.Wait()

> timeout with 2s <nil>       // 到达 work 设置时间退出
> work done context canceled  // 上下文取消退出
```

创建 `context` 后, 将其作为协程参数, 多个协程共用同一个上下文, 实现多个协程消息同步
