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

Golang 的 `context` 包用于创建一个多任务信息同步工具, 在多个协程中设置截止时间, 同步信号, 传递值  
其本质是创建一个通道对象, 并作为多个协程任务参数, 任意一个协程向通道发送数据, 其他协程可以从该通道接收数据或同步信号  
通过操作该对象来控制多个协程的运行  

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

func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc) // 创建超时上下文, 到达超时, Done() 有返回
func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc) // 创建截止时间上下文, 到达截止时间, Done() 有返回
func WithCancel(parent Context) (ctx Context, cancel CancelFunc) // 创建取消上下文, 执行 cancel 函数取消后 Done() 有返回
```

## 超时上下文

设置一个超时时间上下文, 放入多个协程任务, 协程执行时持续监控是否超时, 执行完任务或到达超时后退出

```go
var wg sync.WaitGroup

// 模拟任务, 执行任务时, 循环等待超时
func Task(ctx context.Context, name string, take time.Duration) {
    fmt.Printf("%s need %v\n", name, take)
    finish := time.Now().Add(take)
    for {
        select {
        case <-ctx.Done(): // 监听取消信号
            fmt.Printf("%s work timeout\n", name)
            return
        default:
            if time.Now().After(finish) {
                return
            }
            time.Sleep(time.Second)
        }
    }
}

// 创建超时上下文, 超时后 ctx.Done() 有返回
// WithDeadline 创建的截至时间上下文, 效果和超时上下文类似
ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
defer cancel()

// 创建两个任务, 一个耗时 2s 一个耗时 4s
wg.Add(2)
go func(){
    Task(ctx, "taskUse2", 2*time.Second)
    wg.Done()
}()

go func(){
    Task(ctx, "taskUse4", 4*time.Second)
    wg.Done()
}()

wg.Wait()

// taskUse2 任务 2s 后结束, taskUse4 3s 后超时
taskUse4 need 4s
taskUse2 need 2s
taskUse2 finish
taskUse4 work timeout
```

创建 `context` 后, 将其作为协程参数, 多个协程共用同一个上下文, 实现多个协程消息同步

## 同步取消上下文

```go
var wg sync.WaitGroup
func Task(ctx context.Context, name string) {
    for {
        select {
            case <-ctx.Done(): // 监听取消信号
                fmt.Printf("%s receive cancel at %v\n", name, time.Now())
                return
            default:
                time.Sleep(time.Second)
        }
    }
}

ctx, cancel := context.WithCancel(context.Background())

wg.Add(2)
go func(){
    Task(ctx, "task1")
    wg.Done() 
}()
go func(){
    Task(ctx, "task2")
    wg.Done() 
}()

time.Sleep(time.Second * 2)
fmt.Printf("send cancel at %v\n", time.Now())
cancel()
wg.Wait()

// task 持续监控取消信号, cancel() 后, 任务收到取消信号并退出
send cancel at 2025-12-13 23:08:41.894799153 +0800 CST m=+2.000975644
task2 receive cancel at 2025-12-13 23:08:42.895067677 +0800 CST m=+3.001244148
task1 receive cancel at 2025-12-13 23:08:42.895137388 +0800 CST m=+3.001313859
```

## 上下文传递

上下文 A 可以创建多个子上下文 B, C。 B， C 可以继续创建子上下文 D, E 等, 最终呈现树状结构  
某个节点取消后, 其下所有节点会同步取消, 其上节点不会取消

- 上下文使用键值对存储数据
- 节点从当前节点逐级往上查询, 查询本节点和父节点都有的 key, 优先使用本节点
- 节点只能访问祖先节点数据, 后代节点数据不可见

```go
// 以 ctx0 为根节点, 创建多级子节点
type ctx0Key string
type ctx1Key string
type ctx2Key string

const (
    ctx0K ctx0Key = "ctx0_key"
    ctx1K ctx1Key = "ctx1_key"
    ctx2K ctx2Key = "ctx2_key"

    ctx1K1 ctx1Key = "ctx1_key1"
    ctx1K2 ctx1Key = "ctx1_key2"
)


ctx0 := context.WithValue(context.Background(), ctx0K, "value")
ctx1 := context.WithValue(ctx0, ctx1K, "value")
ctx2 := context.WithValue(ctx1, ctx2K, "value")

// 同节点创建不同数据
ctx1 := context.WithValue(ctx1, ctx1K1, "value")
ctx1 := context.WithValue(ctx1, ctx1K2, "value")

// 节点查询数据
fmt.Println(ctx1.Value(ctx0K))
fmt.Println(ctx2.Value(ctx1K))
```

注: 为避免数据冲突, 应避免使用基本类型作为 key, 可以自定义类型作为 key
