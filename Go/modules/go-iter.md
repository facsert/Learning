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

Go 1.21 新增切片转迭代器的方法

```go
iter := slices.All([]int{0,1,2,3})
for _, v := range iter {
    fmt.Println(v)
}
```

## 示例

```go
package set


type Set[T comparable] struct {
    elems map[T]struct{}
}

func NewSet[T comparable](s ...T) *Set[T] {
    set := &Set[T]{elems: make(map[T]struct{}, len(s))}
    for _, elem := range s {
        set.Add(elem)
    }
    return set
}

// 添加数据
func (s *Set[T]) Add(elem T) {
    s.elems[elem] = struct{}{}
}

// 删除数据
func (s *Set[T]) Remove(elem T) {
    delete(s.elems, elem)
}

// 判断是否存在
func (s *Set[T]) Contains(elem T) bool {
    _, ok := s.elems[elem]
    return ok
}

// 元素数量
func (s *Set[T]) Size() int {
    return len(s.elems)
}

// 是否为空
func (s *Set[T]) IsEmpty() bool {
    return len(s.elems) == 0
}

// 清空
func (s *Set[T]) Clear() {
    s.elems = make(map[T]struct{})
}

// 转为切片
func (s *Set[T]) Values() []T {
    values := make([]T, 0, len(s.elems))
    for elem := range s.elems {
        values = append(values, elem)
    }
    return values
}

// range 遍历所有元素
func (s *Set[T]) All() func(yield func(T) bool) {
    return func(yield func(T) bool) {
        for elem := range s.elems {
            if !yield(elem) {
                return
            }
        }
    }
}


// 并集, set 合并
func (s *Set[T]) Union(other *Set[T]) *Set[T] {
    union := &Set[T]{elems: make(map[T]struct{}, len(s.elems) + len(other.elems))}
    for elem := range s.elems {
        union.Add(elem)
    }
    for elem := range other.elems {
        union.Add(elem)
    }
    return union
}

// 交集, 获取两个 set 共有的元素
func (s *Set[T]) Intersection(other *Set[T]) *Set[T] {
    intersection := &Set[T]{elems: make(map[T]struct{}, len(s.elems))}
    if len(s.elems) > len(other.elems) {
        s, other = other, s
    }
    for elem := range s.elems {
        if other.Contains(elem) {
            intersection.Add(elem)
        }
    }
    return intersection
}

// 差集, 获取 s 中不在 other 中的元素
func (s *Set[T]) Difference(other *Set[T]) *Set[T] {
    difference := &Set[T]{elems: make(map[T]struct{}, len(s.elems))}
    for elem := range s.elems {
        if !other.Contains(elem) {
            difference.Add(elem)
        }
    }
    return difference
}
```
