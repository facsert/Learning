---
pubDatetime: 2022-09-27 15:28:43
title: Go Pointer
slug: Go Pointer
featured: false
draft: false
tags:
  - Go
description: "Go 指针"
---

## Table of Contents

变量赋值: 编译器根据变量类型在内存开辟指定大小的空间写入变量值, 空间的起始地址为变量地址
变量取值: 编译器根据变量类型, 读取一块指定大小且连续地址内容
变量类型决定了变量占用大小

## 指针

- 变量
- 指针变量

|   类型   |  变量名  |  变量值  | 变量存储地址 |
| :------: | :------: | :------: | :----------: |
|   变量   |   name   |   100    |   0x100001   |
| 指针变量 | nameAddr | 0x100111 |   0x100002   |

```go
 strVar := "initial"                             // 初始化变量赋值, 绑定一个固定地址, 值可以变, 地址不变, 类型不变
 Printf("initial strVar value:%v, addr: %p\n", strVar, &strVar)

 strAddr := &strVar                              // 初始化地址类型变量并赋值, 变量绑定地址, 变量值是一个地址
 Printf("initial strAddr value: %v, addr: %p\n", strAddr, &strAddr)

 addrVal := *strAddr                             // 初始化并赋值, 变量绑定地址, 地址类型才能取值
 Printf("initial strVar value:%v, addr: %p\n", addrVal, &addrVal)

 strVar = "modify"

 Printf("modify strVar value:%v, addr: %p\n", strVar, &strVar)
 Printf("modify strAddr value: %v, addr: %p\n", strAddr, &strAddr)
 Printf("modify strVar value:%v, addr: %p\n", addrVal, &addrVal)

 > strVar value:initial, addr: 0xc0000142b0
 > strAddr value: 0xc0000142b0, addr: 0xc000012038
 > addrVal value:initial, addr: 0xc0000142d0

 > strVar value:modify, addr: 0xc0000142b0       // strVar 重新赋值(地址对应的值变化), 地址不变
 > strAddr value: 0xc0000142b0, addr: 0xc000012038
 > addrVal value:initial, addr: 0xc0000142d0     // 地址对应的值没有变化

```

同一个作用域下, 定义变量后, 该变量的地址不变, 地址内的值可变化  

### 变量地址

- 符号: & 获取变量的地址
- 符号: \* 指针变量的值(地址)取值

```go
 var str string = "hello"                        // 定义一个字符串变量 str, 值是 "hello" 地址是 0xc00001a078

 addr := &str                                    // addr 类型为 *string(地址类型), addr 的值是 0xc00001a078(str 地址), addr 地址是 0xc00000e018

 tmp := *addr                                    // 根据 addr 的值(0xc00001a078 str 地址)取值到 "hello" 赋值给 tmp. 等同于 tmp := "hello"

 *addr = "end"                                   // str 的地址不变, addr 指针一直指向 str 的值, 与 str = "end" 效果一致
```

## 指针类型数据

- 值类型: 变量存储的是具体的值, 可以直接使用  
- 引用类型: 值包含一个引用链接, 不保存值

Go 中函数参数本质是值拷贝, 值类型作为参数是拷贝了值, 引用类型则是拷贝了引用, 而拷贝的引用指向同一个值

```go
func main()  {
  v := 1
  fmt.Printf("v: %d\n", v)
  Change(v, &v)
  fmt.Printf("v: %d\n", v)
}

// 在函数修改参数值
// v 是值类型, 本质是创建一个同值的新变量
// p 是指针, 拷贝的指针和原指针共用一个值
func Change(v int, p *int) {
  *p = 3
  v = 2
  fmt.Printf("v: %d, p: %d\n", v, *p)
}

v: 1
v: 2, p: 3
v: 3
```

```go
// range 切片赋值
list := []int{0,1,2,3,4}
for , v := range list {
  list[i] = list[i] * 2
  v = v + 1
}
fmt.Println(list)

// range 循环每次创建一个新变量 v 赋值, 修改 v 不影响切片, 需直接使用 list 按序号赋值
[0 2 4 6 8]


// 切片变更, 注意切片容量是否扩充
// 切片扩充会重新申请更大内存, 切片变量使用新地址
s := []int{0,0,0,0}
fmt.Printf("cap: %d, %v\n", cap(s), s)
Add(s, 3)
fmt.Printf("cap: %d, %v\n", cap(s), s)

func Add(s []int, count int) {
  s[1] = 1
  for range count {
    s = append(s, count)
  }
  s[2] = 2
  fmt.Printf("cap: %d, %v\n", cap(s), s)
}

// 初始切片 s 容量为 4, 地址 addr1, 地址对应的值为 V1 = [0 0 0 0]
// s 作为参数拷贝 add2, 和 addr1 共用值 V1
// 使用 addr2 修改值 V1 为 [0 1 0 0]
// 扩充切片, 新建 addr3 存储值 V2 [0 1 0 0 3 3 3]
// 修改 V2 值 [0 1 2 0 3 3 3]
// 打印 addr1 对应的值 V1 [0 1 0 0]
cap: 4, [0 0 0 0]
cap: 8, [0 1 2 0 3 3 3]
cap: 4, [0 1 0 0]
```
