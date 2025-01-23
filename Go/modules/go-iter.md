---
pubDatetime: 2024-11-25 20:12:54
title: Go iter
slug: Go iter
featured: false
draft: false
tags:
  - Go
description: "Go 迭代器"
---

## 介绍

Go 语言提供了多种迭代器，包括数组、切片、map、通道等

## 自定义迭代器

```go
// 定义迭代器函数类型(待实现)
type yieldFunc func(int) bool

// 定义 IterFunc 类型, 接收 yieldFunc 函数作为参数
type IterFunc func(yield yieldFunc)

// 定义 List 函数, 接收切片作为参数, 返回一个 IterFunc 函数
func List(s []int) IterFunc {
    fn := func(yield yieldFunc) {
        for _, v := range s {
            if !yield(v) { return }
        }
    }
    return fn
}

// 实现一个 yieldFunc 函数, 打印传入的值, 并返回 true
var yielder yieldFunc = func(v int) bool {
    fmt.Println(v)
    return true
}

// 调用 List 函数, 传入切片, 切片保存到 IterFunc 函数中被返回
iter := List([]int{1, 2, 3, 4})

// 传入 yielder 执行函数, 每次循环将切片元素作为 yieldFunc 函数的参数执行
iter(yielder)

> 1
> 2
> 3
> 4
> 5

// 简化版
func List(s []int) func(yield func(int)bool) {
    return func(yield func(int) bool) {
        for _, v := range s {
            if !yield(v) { return}
        }
    }
}

// 调用 List 函数(List 调用)
List([]int{1, 2, 3, 4})(func(v int) bool {
    fmt.Println(v)
    return true
})

> 1
> 2
> 3
> 4

// Go 使用 range 作为语法糖简化以上调用(range 调用)
// Go 编译器会自动将 range 调用转换为 List 调用代码
// 所以 range 参数是迭代对象, range 内部代码即 yield 函数的实现
for  v := range List([]int{1, 2, 3, 4}) {
    fmt.Println(v)
}

// 使用官方的 iter 包
// Go 官方iter 包 Seq 类型定义
type Seq[V any] func(yield func(V) bool)

func IterFunc[V any](s []V) iter.Seq[V] {
    return func(yield func(V) bool) {
        for _, v := range s {
            if !yield(v) { return }
        }
    }
}

for v := range IterFunc([]int{1, 2, 3, 4}) {
    fmt.Println(v)
}
```
