---
pubDatetime: 2023-11-18 18:30:59
title: NodeJS export
slug: NodeJS export
featured: false
draft: false
tags:
  - NodeJS
description: "NodeJS 模块引入和导出"
---

## Table of Contents

javascript 划分了 commonjs 模块系统和 EMCAScript 模块系统, 两种系统的导出和引入方式不同
commonjs 主要用于服务端即后端开发环境  
EMCAScript 主要用于前端开发环境

## Commonjs

- module.exports 创建模块的对外内容
- require 引入其他模块
- 同步加载, 所有模块加载完后执行

CommonJS 模块是 NodeJS 专用, 与 ES6 模块不兼容但 NodeJS 支持 ES6 模块  
NodeJS 默认使用 CommonJS 模块

```js
// module.js 定义变量
var s = "commonjs variable";
var num = 3;
var add = (n) => n + 1;

// 设置可被外部使用的变量, 可重命名
module.exports = {
  s: s,
  num: num,
  add: add,
};
```

```js
// use.js 引入外部模块内容
const m = require("./module.cjs");
console.log(m.s, m.num, m.add);
```

## EMCAScript

- export 创建模块的对外内容
- import 引入其他模块提供的内容
- 异步加载, 加载完立即执行

NodeJS 执行的 ES6 模块文件名必须以 .mjs 结尾, 否则会报错  
若不想改动文件名, 可以在 package.json 文件中配置 `{"type": "module"}`

```js
// 直接使用 node 执行 ES6 模块(使用 import export 导入导出)报错
// (node:47557) Warning: To load an ES module, set "type": "module" in the package.json or use the .mjs extension.

// 文件 module.mjs 声明可引用内容
var s = "ES6 variable";
var num = 3;
var add = (n) => n + 1;

export { s, num, add };
```

```js
// use.mjs 引入外部模块内容
import { s, num, add } from "./module.mjs";
console.log(s, num, add);
```

`export default` 用于指定模块的默认输出, 一个模块只允许一个默认输出  
import 模块的默认输出无需花括号, 且可以重新命名
