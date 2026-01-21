---
pubDatetime: 2023-07-10 14:24:30
title: Go Generics
slug: Go Generics
featured: false
draft: false
tags:
  - Go
description: "Go 泛型"
---

## Table of Contents

泛型可以类型变量化, 可以同一段逻辑复用与多种类型

## 类型约束

```go
// 以下两个函数都是给一个容器添加元素, 但是由于类型不同需要声明两个函数
func AddStr(s []string, i string) []string {
    return append(s, i)
}

func AddInt(s []int, i int) []int {
    return append(s, i)
}


// 使用泛型定义参数类型可以是 int 或 string
// 参数为 int 切片时, T 为 int; 参数是 string 切片, T 为 string, 由实参类型确定
func Add[T int|string](s []T, i T) []T {
    return append(s, i)
}

// 使用时可指定, 也可自动推导
Add[int]([]int{0,1}, 2)
Add([]string{"1st"}, "2nd")
```

## comparable and ordered

comparable(可比较) 和 可排序(ordered)

官方定义

```go
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
