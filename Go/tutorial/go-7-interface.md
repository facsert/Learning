---
pubDatetime: 2023-03-29 15:28:43
title: 07.Go Interface
slug: 07.Go Interface
featured: false
draft: false
tags:
  - Go
description: "Go 接口"
---

## 介绍

接口定义了一种具有特定方法的类型, 任意对象实现了接口定义的所有方法则该对象可以视为接口类型数据  
定义具有充电和放电功能的设备为电器, 则任意同时支持充电和放电的设备均可被认为是电器

## 示例

```go
// 创建电器接口
// Charge 充电一段时间返回电量百分比
// DisCharge 放电一段时间返回电量百分比
// Power 查看电量百分比
type ElectMachine interface {
    Charge(time.Duration) int
    DisCharge(time.Duration) int
    Power() int
}

// 定义 Phone 类型数据实现 ElectMachine 接口所有方法
type Phone struct {
    power int
}

func NewPhone() *Phone {
    return &Phone{ power: 100 }
}

func (p *Phone)Power() int {
    return p.power
}

func (p *Phone)Charge(t time.Duration) int {
    if t >= 100 * time.Minute || p.power == 100 {
        p.power = 100
        return 100
    }
    p.power = min(int(t.Minutes())+p.power, 100)
    return p.power
}

func (p *Phone)DisCharge(t time.Duration) int {
    if t >= 100 * time.Minute || p.power == 0 {
        p.power = 0
        return 0
    }
    p.power = max(p.power-int(t.Minutes()), 0)
    return p.power
}

// ElectMachine 不包含此方法
// 当 Phone 作为 ElectMachine 类型时, 该方法不可用
func (p *Phone)Chat(name string) {
    fmt.Printf("chat with %s\n", name)
}


// 函数接受 ElectMachine 类型数据执行
func Play(m ElectMachine) {
    m.Charge(time.Minute * 60)
    fmt.Printf("after charge power: %d\n", m.Power())
    for m.Power() > 0 {
        m.DisCharge(30 * time.Minute)
        fmt.Printf("play 30 minute power: %d\n", m.Power())
    }
    fmt.Printf("power: %d\n", m.Power())
}

// Phone 实现了 ElectMachine 接口所有方法, 可视为 ElectMachine 类型
// Phone 在作为 ElectMachine 类型期间只能使用 ElectMachine 接口方法
p := NewPhone()
Play(p)

after charge power: 100
play 30 minute power: 70
play 30 minute power: 40
play 30 minute power: 10
play 30 minute power: 0
power: 0
```

## 标准库接口

`fmt` 包

```go
// fmt 接口
// fmt 将变量转换字符打印按以下接口优先级

// 最高优先级
type Formatter interface {
    Format(f State, verb rune)
}

// %#v 占位符打印
type GoStringer interface {
    GoString() string
}

// %v %s
type error interface {
    Error() string
}

// %v %s
type Stringer interface {
    String() string
}

// reflect 打印
```

```go
type Words struct {}

func (w Words) GoString() string {
    return "GoString #%v"
}

func (w Words) Error() string {
    return "Error"
}

func (w Words) String() string {
    return "Stringer %v"
}

w := Words{}
fmt.Printf("%s\n%v\n%#v\n", w, w, w)

Error
Error
GoString #%v

// 注释 Error()
Stringer %v
Stringer %v
GoString #%v
```

```go
// sort 包定义 Interface 接口
type Interface interface {
    Len() int
    Less(i, j int) bool
    Swap(i, j int)
}

// 排序函数
func Sort(data Interface)
```

```go
type Star struct {
    Name string
    Size int
}

type Stars []Star

func (s Stars) Len() int {
    return len(s)
}

func (s Stars) Less(i, j int) bool {
    return s[i].Size < s[j].Size
}

func (s Stars) Swap(i, j int) {
    s[i], s[j] = s[j], s[i]
}


stars := Stars{
    {"4", 4},
    {"1", 1},
    {"3", 3},
    {"2", 2},
}

sort.Sort(stars)
for _, s := range stars {
    fmt.Println(s)
}

{1 1}
{2 2}
{3 3}
{4 4}
```
