---
author: facsert
pubDatetime: 2024-03-11 16:36:43
title: NodeJS function
slug: NodeJS function
featured: false
draft: false
tags:
  - NodeJS
description: "NodeJS 函数"
---

## 基本函数

```js
// 标准函数声明
function add(num) {
    return num + 1;
}

// 箭头函数
const add = (num) => num + 1;
```

## 参数

```js
// 函数设置默认值
function add(num, step=1) {
    return num + step;
}

add(3)                                           // 4, 使用默认值
add(3, 2)                                        // 5, 重写默认值
add(step=2)                                      // 3, 等同于 add(2) 不支持参数名指定参数
```

```js
// rest 参数
function sum(...nums) {
    let m = 0;
    nums.forEach(n => m += n);
    return m;
}

console.log(sum(1,2,3,4))                        // 10, 遍历参数求和
console.log(sum(...[1,2,3,4]))                   // 10, 等同于上, 将数组解包
```
