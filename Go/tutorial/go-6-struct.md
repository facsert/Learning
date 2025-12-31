---
pubDatetime: 2023-03-27 15:28:43
title: 06.Go Struct
slug: 06.Go Struct
featured: false
draft: false
tags:
  - Go
description: "Go 结构体"
---

## Table of Contents

## Struct

### struct 初始化

```go
 type <struct name> struct {                     // 定义结构体, 结构体可以认为是自定义的数据类型
   <attribute name>  <type>                      // 定义结构体属性及其类型
   <attribute name>  <type>                      // 每个属性名唯一, 不能重复
   ...
 }

 type Student struct {                           // 定义 Student 类型数据
   name, city  string                            // Student 类型有 name city 属性, 属性值为 string
   age int                                       // Student 类型有 age 属性, 属性类型为 int
 }

 var John Student                                // 初始化 Student 类型变量 John
 John.name = "John"                              // 使用 . 赋值
 John.age = 18

 lily := Student{                                // 初始化 Student 类型变量 Lily
   name: "Lily",                                 // 顺序可以变更
   city: "shanghai",                             // 部分字段可不填，自动使用类型零值
 }

 bob := Student{                                 // 可以按顺序只写值，此时必须按顺序，且所有字段都需要填值
  "bob",
  "xian",
 }

 Printf("%#v\n", John)
 Printf("%#v\n", Lily)

 > main.Student{name:"John", city:"", age:18}    // 未初始化的属性使用类型的零值
 > main.Student{name:"Lily", city:"shanghai", age:0}
```

### struct 方法

```go
 type Student struct {
   name, city string
   age int
 }

 func (s Student) Learn() {
   Printf("%s learning in %s\n", s.name, s.city)
 }

 func (s Student) Play() {
   Printf("%s play games\n", s.name)
 }

 John := Student{
    name: "John",
    city: "shanghai",
 }

 John.Learn()
 John.Play()

 > John learning in shanghai
 > John play games
```

### struct 属性修改

```go
 type Student struct {
   name, city string
   age int
 }

 func (s Student) rename1(name string) {         // 重命名属性 name
   s.name = name
   Printf("rename1 to %s\n", s.name)
 }

 func (s *Student) rename2(name string) {        // 重命名属性 name
   s.name = name
   Printf("rename2 to %s\n", s.name)
 }

 John := Student{
    name: "John",
    city: "shanghai",
 }

 John.rename2("Jack")
 John.rename1("Lily")
 Printf("name: %s\n", John.name)

 > rename2 to Jack                               // 重命名结果对结构体生效
 > rename1 to Lily                               // 重命名结果只在函数内生效
 > name: Jack
```

### struct 转 json

```go
import (
   . "fmt"
   "encoding/json"
)

 type Student struct {
     Name string                                 // 属性名大写其它模块可访问, 可以转 json
     city string                                 // 属性名小写其它模块不能访问也不能转 json
     Age int     `json:"age"`                    // 可以通过定义 tag, 修改转化为 json 后 key 名称
}

 John := Student{
    Name: "John",
    city: "shanghai",
    Age: 18,
 }

 jsonStu, err := json.Marshal(John)              // struct 转 json
 if err != nil {
   panic("json marshal failed")
 }

 Println(John)
 Println(string(jsonStu))

 > {John shanghai 18}                            // John 结构体所有属性值
 > {"Name":"John","age":18}                      // city 属性不在, Age 属性名变为 age
```

## 结构体示例

```go
type Person struct {
  Name string
  Age int
  Msg string
}

func NewPerson(name string, age int, Msg) *Person {
  return &Person{
    Name: name,
    Age: age,
    Msg: msg
  }
}

func (p *Person)SetName(name string) *Person {
  p.Name = name
  return p
}

func (p *Person)SetAge(age int) *Person {
  p.Age = age
  return p
}

func (p *Person)SetMsg(msg string) *Person {
  p.Msg = msg
  return p
}

p1 := NewPerson("John", 18, "boy")               // 一般结构体都会有对应的构建函数, 用于直接生成结构体
p2 := (&Person{}).SetName("lily").SetAge(18)     // 生成空结构体, 可选择是否添加属性值或重新赋值
```
