---
pubDatetime: 2023-05-23 15:28:43
title: Go Goroutine
slug: Go Goroutine
featured: false
draft: false
tags:
  - Go
description: "Go 协程"
---

## Table of Contents

## 协程

golang 支持并发和并行, 可以同时跑多个协程
golang 使用 `go <function name>()` 创建并执行协程
协程本质是一个函数

```go
import (
    . "fmt"
    "time"
    "sync"
)

var wg sync.WaitGroup                            // 定义 wg 辅助协程计数和执行

func main() {
    Println(time.Now())                          // 执行前打印时间

    wg.Add(2)                                    // 标记需要执行两个协程
    go func() {                                  // 新建协程, 类似于将函数放后台执行, main 直接接执行下一个函数
        cook()
        wg.Done()                                // 标记协程执行完毕
    }

    go func() {
        wash()
        wg.Done()
    }

    wg.Wait()                                    // 等待所有的协程执行完成
    Println(time.Now())                          // 所有协程执行完后打印时间
}

func cook() {
    time.Sleep(time.Second * 3)                  // sleep 3s
    Println("cook by machine use 3s")
}

func wash() {
    time.Sleep(time.Second * 2)
    Println("wash close by machine use 2s")
}

> 2023-04-01 21:51:20.692681383 +0800 CST m=+0.000016384
> wash close by machine use 2s
> cook by machine use 3s
> 2023-04-01 21:51:23.693482859 +0800 CST m=+3.000817861
```

golang 通过新建 2 个协程同时跑 main cook wash 函数, 3 个函数并行执行共花费 3s.
main 也是一个协程, main 中若不设置 wg.Wait() 等待其余协程完成, main 执行完成后会关闭所有协程

## channel

协程往往用于多个类似独立事件, 协程一般不设置返回值, 而是设置一个通道, 多个协程往同一个通道写入数据, 通道是队列(先进先出), 之后从通道取出

### 无缓冲通道

通道容量为 0, 不能存值, 发送语句和结束语句需要都执行, 否则一方会一直等待另一方导致阻塞  
发送语句先执行, 则发送语句阻塞, 等待接收语完后继续执行  
接受语句先执行, 则接受语句阻塞, 等待发送语完后继续执行  
以上特性常用于协程同步, 无缓冲通道也被称为同步通道

```go
import (
    . "fmt"
    "time"
    "sync"
)

var wg sync.WaitGroup

func main() {
    Printf("start at %v\n", time.Now())
    ch := make(chan int, 0)                      // 初始化通道, 设置缓冲容量为 0
    wg.Add(1)
    go receive(ch)                               // 创建协程
    ch <- 3                                      // 向通道写入数据, 等待接收语句执行, 数据接收后执行下一句
    Printf("send at %v\n", time.Now())
}

func receive(c chan int) {
    time.Sleep(time.Second * 2)
    rec := <- c                                  // 接收通道数据, 若先执行则等待发送语句执行
    Printf("receive %d at %v\n", rec, time.Now())// 发送和接收执行同步
    wg.Done()
}

> start at 2023-04-01 22:52:35.842840438 +0800 CST m=+0.000018823
> receive 3 at 2023-04-01 22:52:37.843993741 +0800 CST m=+2.001172157
> send at 2023-04-01 22:52:37.844102485 +0800 CST m=+2.001280900
```

### 有缓冲通道

通道容量大于 0, 通道允许在容量未满时存值, 容量存满时变成无缓冲通道

```go
import (
    . "fmt"
    "time"
    "sync"
)

var wg sync.WaitGroup

func main() {
    Printf("start at %v\n", time.Now())
    ch := make(chan int, 2)                      // 初始化通道, 设置缓冲容量为 2
    wg.Add(2)
    go receive(ch)                               // 创建协程接收通道的值
    go send(ch)                                  // 创建协程向通道传值
    wg.Wait()
    Printf("over at %v\n", time.Now())
}

func send(ch chan int) {
    ch <- 1
    ch <- 2
    close(ch)                                    // 向通道传值结束后关闭通道
    Printf("send 1,2 at %v\n", time.Now())
    wg.Done()
}

func receive(ch chan int) {
    time.Sleep(time.Second * 1)
    rec := []int{}
    for i := range ch {                          // 通道关闭后, 使用 range 读取通道值
        rec = append(rec, i)
    }
    Printf("receive %v at %v\n", rec, time.Now())// 发送和接收执行同步
    wg.Done()
}

> start at 2023-04-02 12:20:34.69317803 +0800 CST m=+0.000016435
> send 1,2 at 2023-04-02 12:20:34.693231007 +0800 CST m=+0.000069413
> receive [1 2] at 2023-04-02 12:20:35.693311209 +0800 CST m=+1.000149614
> over at 2023-04-02 12:20:35.693371227 +0800 CST m=+1.000209634
```

### 示例

设置 Read 函数完成独立事件

```go
func main()  {
    var wg sync.WaitGroup
    books := []string{
        "math",
        "physics",
        "science",
        "biology",
    }

    ch := make(chan string, 10)  // 设置缓冲通道存储多个协程传输的数据
    
    done := make(chan struct{})  // 设置 done 等待 Learn 完成后才能退出
    defer close(done)            // 退出前关闭通道

    go func() {                  // 设置协程后台持续从通道取出数据
        Learn(ch)
        done <- struct{}{}       // 执行完 Learn 后, 写入空结构体表示 Learn 完成
    }()

    for _, book := range books { // 创建协程处理多个独立事件
        wg.Add(1)
        go func() {              // 若独立事件数量过多, 会导致协程数量过多, 一般会限制协程池数量
            defer wg.Done()
            Read(book, ch)
        }()
    }

    wg.Wait()                    // 等待所有独立事件完成
    close(ch)                    // 关闭数据通道, Learn 取出所有数据后结束 range 循环
    <- done                      // 从 done 取出数据, 无数据则等待, 等待 Learn 结束的 done 信号
}

func Read(book string, ch chan string) {
    ch <- fmt.Sprintf("%v read %s", time.Now(), book)
}

func Learn(ch chan string) {
    for s := range ch {          // 从通道取出数据, 无数据则等待, 通道关闭且无数据后退出循环
        fmt.Println(s)
    }
    fmt.Println("finish learning")
}


2025-12-31 17:39:52.393453547 +0800 CST m=+0.000049012 read biology
2025-12-31 17:39:52.393556174 +0800 CST m=+0.000151638 read science
2025-12-31 17:39:52.393475591 +0800 CST m=+0.000071055 read math
2025-12-31 17:39:52.393488747 +0800 CST m=+0.000084219 read physics
finish learning
```

设置协程池控制协程数量  
使用缓存通道的机制, 设置一个指定数量的缓存通道, 每创建一个协程则从通道取出一个值, 执行完一个协程则写入一个值  
通道值取完表示协程数量达到上限, 需要等待有协程完成写入值才能再次取值，如此保证协程数量超过阈值

```go
func main()  {
    var wg sync.WaitGroup
    tokens := make(chan struct{}, 3)  // 设置协程池数量, 同时可存在最大协程数量
    for range 3 {
        tokens <- struct{}{}          // 初始化, 创建令牌
    }

    for index := range 10 {
        <- tokens                     // 每次取出一个令牌, 创建一个协程, 令牌用完则等待
        wg.Add(1)
        go func() {
            defer wg.Done()
            Doing(index)
            tokens <- struct{}{}      // 协程执行完成, 返回令牌
        }()
    }
    wg.Wait()
}

func Doing(index int) {
    fmt.Printf("%d %v doing something\n", index, time.Now().Format("15:04:05"))
    time.Sleep(1 * time.Second)
}

2 18:00:13 doing something
0 18:00:13 doing something
1 18:00:13 doing something
5 18:00:14 doing something
3 18:00:14 doing something
4 18:00:14 doing something
6 18:00:15 doing something
8 18:00:15 doing something
7 18:00:15 doing something
9 18:00:16 doing something
```

## 锁

Go 中一些操作不是并发安全的, 需要锁保证同一时间一个资源只能被一个协程操作

```go
import (
    "fmt"
    "sync"
)

func main() {
    var wg sync.WaitGroup
    var str string

    add := func() {
        for i := 0; i < 500; i++ {
            str += "a"
        }
        wg.Done()
    }

    wg.Add(2)
    go add()
    go add()

    wg.Wait()
    fmt.Printf("str: %d\n", len(str))
}

$ go run .\main.go
> str: 827
```

两个协程同时操作 `str`, 存在数据竞争, 结果互相覆盖和预期不符

```go
import (
    "fmt"
    "sync"
)

func main() {
    var wg sync.WaitGroup
    var mu sync.Mutex
    var str string

    add := func() {
        for i := 0; i < 500; i++ {
            mu.Lock()
            str += "a"
            mu.Unlock()
        }
        wg.Done()
    }

    wg.Add(2)
    go add()
    go add()

    wg.Wait()
    fmt.Printf("str: %d\n", len(str))
}

$ go run .\main.go
> str: 1000
```

使用互斥锁, 保证同一时间资源只被一个协程操作

## 协程池

```go
type Limiter struct {
    ch chan struct{}
}

func NewLimit(cap int) *Limiter {
    if cap <= 0 {
        cap = 1
    }
    l := &Limiter{ch: make(chan struct{}, cap)}
    for range cap {
        l.ch <- struct{}{}
    }
    return l
}

func (l *Limiter) Acq() {
    <-l.ch
}

func (l *Limiter) Rel() {
    l.ch <- struct{}{}
}

func (l *Limiter) Close() {
    close(l.ch)
}


func main() {
    var wg sync.WaitGroup
    limiter := NewLimit(3)

    for i := range 10 {
        limiter.Acq()  // 限制器获取锁
        wg.Add(1)
        go func(index int) {
            defer limiter.Rel() // 释放锁
            defer wg.Done()
            fmt.Printf("task: %d at %v\n", index, time.Now())
            time.Sleep(time.Second)
        }(i)
    }
    
    wg.Wait()
}

// 创建限制器, 设置最大协程数量为 3
// 循环创建 10 个任务, 模拟每个任务耗时 1s
// 创建任务前先获取锁, 获取锁成功后创建任务并执行
// 锁耗尽后, limiter.Acq() 阻塞, 直到有任务释放锁
task: 2 at 2025-12-13 21:59:55.863158944 +0800 CST m=+0.000259869
task: 0 at 2025-12-13 21:59:55.863176467 +0800 CST m=+0.000277422
task: 1 at 2025-12-13 21:59:55.863190604 +0800 CST m=+0.000291569
task: 3 at 2025-12-13 21:59:56.863754037 +0800 CST m=+1.000855002
task: 4 at 2025-12-13 21:59:56.863879653 +0800 CST m=+1.000980618
task: 5 at 2025-12-13 21:59:56.863891785 +0800 CST m=+1.000992751
task: 6 at 2025-12-13 21:59:57.864801951 +0800 CST m=+2.001902916
task: 7 at 2025-12-13 21:59:57.864985696 +0800 CST m=+2.002086661
task: 8 at 2025-12-13 21:59:57.86501972 +0800 CST m=+2.002120685
task: 9 at 2025-12-13 21:59:58.865207246 +0800 CST m=+3.002308212
```
