---
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

## JSX

- JSX 是返回 HTML 标签的函数
- JSX 组件函数只能返回单个组件
- 组件函数开头大写

```js
import React from "react";

function Component() {
  return (
    <div>
      <p>JSX Component</p>
    </div>
  );
}
```

## Props

JSX 参数

- `<Component arg1="" arg2=""><Component>` 在标签内部可自定义参数
- `<Component><p>parma</p><Component>` 标签包含的内容固定为 `children`

```js
// 所有参数都存放在 props 中
function Child(props) {
  return (
    <div>
      <p>{props.messages}</p>
      {props.children}
    </div>
  );
}

function Parent() {
  const messages = "parent message";
  return (
    <Child messages={messages}>
      <p>parent html</p>
    </Child>
  );
}

// 使用解构参数
function Card({ name, value }: { name: string, value: number }) {
  return (
    <div>
      <p>name: {name}</p>
      <p>value: {value}</p>
    </div>
  );
}

function Page() {
  return (
    <div>
      <Card name="card1" value={1} />
      <Card name="card2" value={2} />
      <Card name="card3" value={3} />
    </div>
  );
}
```

## 数据持久化

```js
// localStorage 设置数据(仅能保存字符串)
localStorage.setItem("key", "value");

let students = [
  { id: 1, name: "John" },
  { id: 2, name: "Jane" },
];
localStorage.setItem("students", JSON.stringify(students));

// localStorage 获取数据(若字段不存在返回 null)
let value = localStorage.getItem("key");
value = value ? JSON.parse(value) : "key is null";
```
