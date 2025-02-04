---
pubDatetime: 2024-06-06 21:51:27
title: Mermaid basic
slug: Mermaid basic
featured: false
draft: false
tags:
  - Mermaid
description: "Mermaid basic"
---

## Table of Contents

## 介绍

mermaid 是一个开源的图表和流程图生成工具，可以生成多种类型的图表  
mermaid 可以在 markdown 和 html 中使用  
[Mermaid 官网](https://mermaid.nodejs.cn/)

```mermaid
---
title: markdown 示例
---
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

```html
<!-- html mermaid 示例 -->
<html>
  <body>
    Here is one mermaid diagram:
    <pre class="mermaid">
            graph TD 
            A --> B
            A --> C
            B --> D
            C --> D
    </pre>
    <script type="module">
      import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
      mermaid.initialize({ startOnLoad: true });
    </script>
  </body>
</html>
```

## 语法

### 图

```mermaid
---
title: 流程图 flowchat/graph
---
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

```mermaid
---
title: 时序图 sequenceDiagram
---
sequenceDiagram
    Alice->>John: Hello John, how are you?
    John-->>Alice: Great!
    Alice-)John: See you later!
```

```mermaid
---
title: 状态图 stateDiagram
---
stateDiagram-v2
    [*] --> Still
    Still --> [*]

    Still --> Moving
    Moving --> Still
    Moving --> Crash
    Crash --> [*]
```

```mermaid
---
title: 饼图 pie
---
pie showData
    title Pets adopted by volunteers
    "Dogs" : 386
    "Cats" : 85
    "Rats" : 15
```

### 图形

```mermaid
---
title: markdown 形状
---

flowchart LR;
    A <--> B[box]
    C{diamond} <--> D(circle)
    E([round-md]) <--> F((round))
    G[(Database)]
```

### 线

```mermaid
---
title: markdown 线
---

flowchart LR;
    A---A1;
    A-- text -->A2;
    A-->A3;

    B-.-B1;
    B-.->B2;
    B-. text .-B3;

    C & D --> E & F;

    H --x I;
    H <--> I;
```
