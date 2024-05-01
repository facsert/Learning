---
author: facsert
pubDatetime: 2024-04-29 22:39:27
title: React bae
slug: React bae
featured: false
draft: false
tags:
  - React
description: "React base"
---

## Table of Contents

## Props

```js
function Parent() {
    const messages = "parent message"
    return (
        <Child messages={messages} />
    )
}

function Child(props) {
    return (
        <p>{props.messages}</p>
    )
}
```
