---
author: facsert
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

`const [state, setState] = useState(defaultValue)`  
只允许使用 `setState` 函数变更 `state`, 且状态变更后, 组件会重新渲染  

```ts
import { useState } from 'react';

// 计数器
export default function Counter() {
    const [count, setCount] = useState(0);
    return (
        <button onClick={() => setCount(count + 1)}></button>
    );
}

// 开关
export default function Switch() {
    const [open, setOpen] = useState(true);
    return (
        <button onClick={() => setCount(!open)}></button>
    );
}
```

```js
function HomePage() {
    const [open, setOpen] = useState(true);
    return (
        <div>
            <header>
                <Menu open={open} setOpen={setOpen}/>
            </header>
            <main>
                <Sidebar open={open} />
            </main>
        </div>
    )
}

function Menu({open, setOpen}) {
    return (
        <div>
            <button onClick={()=>{setOpen(!open)}}></button>
        </div>
    )
}

function Sidebar({open}) {
    const sidebar = {
        display: open ? "block" : "none"
    };
    return (
        <div style={sidebar}>
            ...
        </div>
    )
}
```

## useEffect

```js

```
