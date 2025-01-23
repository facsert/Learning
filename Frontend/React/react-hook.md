---
pubDatetime: 2024-04-29 22:39:27
title: React hook
slug: React hook
featured: false
draft: false
tags:
  - React
description: "React hooks"
---

## Table of Contents

## useState

状态控制钩子, 创建状态值和变更函数  
状态和变更函数可以作为参数传递给其它组件以跨组件通信

`useState` 是 React 一个 hook, 可以创建一个状态变量和设置状态函数  
`const [status, setStatus] = useState<T>(StatusDefault);`

- 只能使用 setStatus 改变 status 的值
- status 变更后会触发组件重新渲染
- status 和 setStatus 可以传递给其他组件达到跨组件通信
- set 函数是异步执行, 不会阻塞组件渲染, 但可能会延迟状态更新

```ts
import { useState } from "react";

// 计数器
export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}></button>;

  // set 函数可以添加参数获取上一次状态
  // const [count, setCount] = useState(0);
  // return <button onClick={() => setCount(pre => pre + 1)}></button>;
}

// 开关
export default function Switch() {
  const [open, setOpen] = useState(true);
  return <button onClick={() => setCount(!open)}></button>;
}
```

### 跨组件通信

通过传递状态变量和设置状态函数来传递状态

```ts
// Home Page
function HomePage() {
  const [dark, setDark] = useState<boolean>(true);
  return (
    ...
    <Menu dark={dark} setDark={setDark} />
    <SideBar dark={dark} />
    ...
  )
}

// Menu Component
function Menu({ dark, setDark }: { dark: boolean, setDark: (dark: boolean) => void }) {
  return (
    <>
      ...
      // 切换主题
      <button onClick={() => setDark(!dark)}>{dark? "Dark": "Light"}</button>
      ...
    </>
  )
}

// SideBar Component
function SideBar({ dark }: { dark: boolean }) {
    return (
      // Menu 设置 dark 属性, 触发 SideBar 组件重新渲染
      <div className={dark ? 'dark' : 'light'}>
      ...
      </div>
    )
}
```

## useEffect

`useEffect` 是 React 一个 hook, 可以在组件渲染后执行一些操作  
`useEffect(() => { ... }, [])`

- useEffect 的第一个参数是一个函数, 该函数会在组件渲染后执行
- useEffect 的第二个参数是一个数组, 该数组中的元素会作为依赖项, 当依赖项发生变化时, useEffect 会再次执行

```ts
import { useEffect } from 'react';
export default function Counter() {
  useEffect(() => {
    console.log('组件渲染后执行');
    return () => {
        console.log('组件销毁时执行');
    }
    // 依赖项发生变化时执行

  }, [])
  ...
}
```
