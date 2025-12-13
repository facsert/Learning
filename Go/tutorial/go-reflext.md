---
pubDatetime: 2025-12-13 12:35:43
title: Go Reflect
featured: false
draft: false
tags:
  - Go
description: "Go Reflect"
---

## 介绍

Golang `reflect` 包是一个反射包，用于在运行时检查变量的类型和值  
常用于获取变量类型, 值, 方法等信息

## 使用

- reflect.TypeOf(v any) reflect.Type 获取变量类型
- reflect.ValueOf(v any) reflect.Value 获取变量值

```go
type Type interface {
  Name() string                    // 获取变量名
  Kind() reflect.Kind              // 获取变量类型
  Elem() reflect.Type              // 若 t 是指针类型, 返回被指针指向的类型
  Field(i int) reflect.StructField // 获取结构体底 i 个字段
  NumField() int                   // 获取结构体字段数量
}

type Value struct{}

func (v Value) Elem() Value                   // 若 v 是指针类型, 返回被指针指向的变量
func (v Value) Field(i int) Value             // 按结构体字段索引获取结构体字段值
func (v Value) FieldByName(name string) Value // 按结构体字段名获取结构体字段
func (v Value) IsNil() bool                   // 判断 v 是否为 nil
func (v Value) IsZero() bool                  // 判断 v 是否为零值
func (v Value) Interface() (i any)            // 获取变量值, 转为 any

```

```go

import (
    "fmt"
    "reflect"
)

type Person struct {
    Name string
    Age int
    Address string
}

// 获取结构体的字段名列表
func StructKeys(s any) []string {
    t := reflect.TypeOf(s)
    keys := make([]string, 0, t.NumField())
    for i := 0; i < t.NumField(); i++ {
        keys = append(keys, t.Field(i).Name)
    }
    return keys
}

func StructMap(s any) map[string]string {
    t := reflect.TypeOf(s)
    v := reflect.ValueOf(s)

    m := make(map[string]string, t.NumField())
    for i := 0; i < t.NumField(); i++ {
        m[t.Field(i).Name] = fmt.Sprintf("%s: %v", v.Field(i).Kind(), v.Field(i).Interface())
    }
    return m
}

func main() {
    p := Person{Name: "John", Age: 30, Address: "New York"}
    keys := StructKeys(p)

    fmt.Println(keys)
    for k, v := range StructMap(p) {
        fmt.Printf("%s: %s\n", k, v)
    }
}

[Name Age Address]
Address: string: New York
Name: string: John
Age: int: 30
```

## 结构体 Tag

Golang `encoding/json` 包, 也是通过反射获取结构体字段信息  
此外, `json.Marshal` 支持 Tag 注解, 可以自定义字段名或忽略字段  
Tag 功能本质是 `reflect.StructTag` 获取 tag 内容按一定规则解析

```go
// 解析 Tag, 
func ParseTag(field reflect.StructField, tag string) string {
    jsonTag := field.Tag.Get(tag)
    if jsonTag == "-" {
      return ""
    }
    name, _, _ := strings.Cut("jsonStr", ",")
    if name == "" {
      return field.Name
    }
    return name
}

type Person struct {
  Name    string `json:"name" tag:"tag_name"`
  Age     int    `json:"age" tag:"tag_age"`
  Address string `json:"address" tag:"-"`
}

p := Person{"John", 18, "HK"}
t := reflect.TypeOf(p)

for i := 0; i< t.NumField(); i++ {
  fmt.Printf("name: %s\n", ParseTag(t.Field(i), "tag"))
}

s, _ := json.Marshal(p)
fmt.Printf("json: %s\n", string(s))

name: tag_name
name: tag_age
name:
json: {"name":"John","age":18,"address":"HK"}
```

## 代码示例

自定义 `insert` 和 `update` 类型, 用于数据库字段映射  
`json` 字段映射保留标准库的 `-` 忽略规则和 `omitzero` 零值忽略规则  
可自定义 Tag, 解析规则同上  

```go
package comm

import (
    "reflect"
    "strings"
)

type TagType string

var (
    JsonTag   TagType = "json"
    InsertTag TagType = "insert"
    UpdateTag TagType = "update"
)

type fieldTag struct {
    name string
    ignore bool
    omitZero bool
}

// 解析 Tag
func parseTag(field reflect.StructField, tag TagType) *fieldTag {
    jsonTag := field.Tag.Get(string(tag))
    if jsonTag == "-" {
      return &fieldTag{"", true, false}
    }
    name, opts, _ := strings.Cut(jsonTag, ",")
    if strings.TrimSpace(name) == "" {
      name = field.Name
    }
    return &fieldTag{name, false, strings.Contains(opts, "omitzero")}
}

// 确保传入的是结构体类型
func ensureStruct(m any) (reflect.Type, reflect.Value, bool){
    t, v := reflect.TypeOf(m), reflect.ValueOf(m)
    if t.Kind() == reflect.Pointer {
      if v.IsNil() {
        return nil, reflect.Value{}, false
      }
      t, v = t.Elem(), v.Elem()
    }
    if v.Kind() != reflect.Struct {
      return nil, reflect.Value{}, false
    }
    return t, v, true
}

func StructKeys(m any, tags ...TagType) []string {
    t, v, isStruct := ensureStruct(m)
    if !isStruct {
      return nil
    }

    tag := JsonTag
    if len(tags) > 0 {
      tag = tags[0]
    }

    keys := make([]string, 0, t.NumField())
    for index := 0; index < t.NumField(); index++ {
      ft := parseTag(t.Field(index), tag)
      if ft.ignore {
        continue
      }
      if ft.omitZero && v.Field(index).IsZero() {
        continue
      }
      keys = append(keys, ft.name)
    }
    return keys
}

func StructValue(m any, field string) any {
    _, v, isStruct := ensureStruct(m)
    if !isStruct {
        return nil
    }
    return v.FieldByName(field).Interface()
}

func StructMap(m any, tags ...TagType) map[string]any {
    t, v, isStruct := ensureStruct(m)
    if !isStruct {
        return nil
    }

    tag := JsonTag
    if len(tags) > 0 {
      tag = tags[0]
    }

    structMap := make(map[string]any, t.NumField())
    for index := 0; index < t.NumField(); index++ {
      ft := parseTag(t.Field(index), tag)
      if ft.ignore {
        continue
      }
      if ft.omitZero && v.Field(index).IsZero() {
        continue
      }
      structMap[ft.name] = v.Field(index).Interface()
    }
    return structMap
}
```
