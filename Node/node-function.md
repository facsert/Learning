---
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
function add(num, step = 1) {
  return num + step;
}

add(3); // 4, 使用默认值
add(3, 2); // 5, 重写默认值
add((step = 2)); // 3, 等同于 add(2) 不支持参数名指定参数
```

```js
// rest 参数
function sum(...params) {
  let m = 0;
  params.forEach((n) => (m += n));
  return m;
}

console.log(sum(1, 2, 3, 4)); // 10, 遍历参数求和
console.log(sum(...[1, 2, 3, 4])); // 10, 等同于上, 将数组解包
```

## 常用函数

```js
const n1 = [1, 2, 3, 4];

// map 遍历数组, 返回每个元素在执行函数的返回值
const n2 = n1.map((i) => i * i); // n2 [ 1, 4, 9, 16 ]

// filter 筛选数组, 筛选执行函数为 true 的值
const n3 = n1.filter((i) => i % 2 === 0); // n3 [ 2, 4 ]

// reduce 累计函数, 开始两个值, 之后将累计结果和下一个值循环 (1, 2) -> (3, 3) -> (6, 4)
const n4 = n1.reduce((acc, i) => acc + i); // n4 10

// find 查找函数, 返回找到的第一个数据, 不存在返回 undefine
const n = n1.find((i) => i === 5); // n undefine

const box = {
  name: "box",
  width: 10,
  height: 20,
  content: [1, 2, 3],
};

// 对象遍历
Object.keys(box);
Object.values(box);
Object.entries(box).map(([key, value]) => {
  console.log(key, value);
});
// name box
// width 10
// height 20
// content [1,2,3]
```
