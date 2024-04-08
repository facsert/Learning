---
author: facsert
pubDatetime: 2024-03-05 16:11:43
title: CSS flex
slug: CSS flex
featured: false
draft: false
tags:
  - CSS
description: "CSS flex 布局"
---

## Table of Contents

## 介绍

flex 浮动布局是一种强大的布局方式, 用于创建灵活的、响应式的页面布局  
Flex 布局模型提供了一种更加简单, 高效的方式来布局, 对齐, 排序和分布容器中的子元素

## 属性

```css
.contain {
    /* 设置组件为块级组件并使用浮动布局 */
    display: flex;

    /* 定义组件的主轴, 横向左至右, 横向右至左, 纵向上至下, 纵向下至上  */
    flex-direction: row|row-reverse|column|column-reverse;
    
    /* 沿主轴方向排列方式, 主轴起点, 主轴终点, 主轴中心, 主轴均匀分布, 首尾无间隙, 主轴均匀分布, 首尾有间隙  */
    justify-content: flex-start | flex-end | center | space-between | space-around;
    
    /* 垂直主轴方向排列方式, 主轴起点, 主轴终点, 主轴中心, 主轴均匀分布, 首尾无间隙, 主轴均匀分布, 首尾有间隙  */
    align-items: flex-start | flex-end | center | baseline | stretch;
}
```