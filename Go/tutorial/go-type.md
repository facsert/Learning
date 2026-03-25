---
pubDatetime: 2023-07-10 14:24:30
title: Go Typing
slug: Go Typing
featured: false
draft: false
tags:
  - Go
description: "Go 类型/泛型"
---

## Table of Contents

泛型可以将类型变成变量, 使得同一段逻辑复用与多种类型

## 类型约束

Go 是强类型语言, 变量都必须有确定的类型, 导致同样逻辑, 不同类型需重复实现  
使用泛型可以将类型作为形参, 实际使用按执行的入参推导实际类型

```go
// 以下两个函数都是给一个容器添加元素, 但是由于类型不同需要声明两个函数
func AppendStr(s []string, i string) []string {
    return append(s, i)
}

func AppendInt(s []int, i int) []int {
    return append(s, i)
}

// 使用泛型定义参数类型可以是 int 或 string
// 参数为 int 切片时, T 为 int; 参数是 string 切片, T 为 string, 由实参类型确定
func Append1[T int|string](s []T, i T) []T {
    return append(s, i)
}

func Append2[T any](x []T, y T) []T {
    return append(x, y)
}

// 使用时可指定, 也可自动推导
AppendInt[int]([]int{0,1}, 2)
AppendStr([]string{"1st"}, "2nd")

// 参数类型只允许为 int 或 string
Append1([]int{0,1}, 2)

// 参数类型任意
Append2([]int64{0,1}, 2)
```

泛型定义模块

```go
type Stack[T cmp.Ordered] struct {
    queue []T
    tail int
}

func NewStack[T cmp.Ordered](queue []T) *Stack[T] {
    size := 2 << 4
    for size < len(queue) {
        size <<= 1
    }
    q := make([]T, size)
    copy(q, queue)
    return &Stack[T]{
        queue: q,
        tail: len(queue),
    }
}

func (s *Stack[T])Cap() int {
    return len(s.queue)
}

func (s *Stack[T])Len() int {
    return s.tail
}

func (s *Stack[T])Push(elem T) {
    s.grow()
    s.queue[s.tail] = elem
    s.tail++
}

func (s *Stack[T])Pop(elem T) T {
    if s.tail == 0 {
        panic("stack no value left")
    }
    s.tail--
    return s.queue[s.tail]
}

func (s *Stack[T])grow() {
    if s.tail < len(s.queue)-1 {
        return
    }
    q := make([]T, s.tail << 1)
    copy(q, s.queue)
    s.queue = q
}


// 按入参推导出 T 为 int 类型
// 入参支持所有 Ordered 类型
s := NewStack([]int{0,1,2,3,4})
s.Pop()
s.Pop()

4
3
```

## comparable and ordered

comparable(可比较) 和 可排序(ordered)

官方定义

```go
// package cmp
// ~int 表示类型的底层数据是 int, 适用于类型别名或自定义类型
type Ordered interface {
    ~int | ~int8 | ~int16 | ~int32 | ~int64 |
        ~uint | ~uint8 | ~uint16 | ~uint32 | ~uint64 | ~uintptr |
        ~float32 | ~float64 |
        ~string
}

func MaxItem[T cmp.Ordered](x, y T) T {
    if x > y {
        return x
    }
    return y
}

// 支持多种可排序类型
MaxItem(2, 4)
MaxItem(3.01, 2.111)
MaxItem("aaa", "ccc")
```
