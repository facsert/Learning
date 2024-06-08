---
author: facsert
pubDatetime: 2024-04-29 22:39:27
title: React base
slug: React base
featured: false
draft: false
tags:
  - React
description: "React base"
---

## Table of Contents

## jsx

```js
import React from 'react'

function Component() {
    return (
        <div>
        <p>JSX Component</p>
        </div>
    )
}
```

## Props

```js
function Parent() {
    const messages = "parent message"
    return (
        <Child messages={messages}><p>parent p html</p> </Child>
    )
}

function Child(props) {
    return (
        <div>
        <p>{props.messages}</p>
        {props.children}
        </div>
    )
}
```

## 数据持久化

```js
// localStorage 设置数据(仅能保存字符串)
localStorage.setItem('key', 'value')

let students = [
    {id: 1, name: 'John'},
    {id: 2, name: 'Jane'},
]
localStorage.setItem('students', JSON.stringify(students))

// localStorage 获取数据(若字段不存在返回 null)
let value = localStorage.getItem('key')
value = value ? JSON.parse(value) : "key is null"
```
