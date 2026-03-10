---
pubDatetime: 2026-03-04 16:36:08
title: Ai LangChain
featured: false
draft: false
tags:
  - Ai
description: "Ai LangChain"
---

## 介绍

[LangChain](https://www.langchain.com/) 是一个开源的大模型智能体开发框架, 支持主流大模型  

- LangChain 短流程简单任务
- LangGraph 固定且长流程任务
- DeepAgent 开放式的复杂任务

## LangChain

`LangChain` 用于创建简单任务智能体, 可配置置可用工具, 短期记忆

- Model 大模型对象
- Messages 上下文内容
- Tools 定义智能体可用的工具
- Agent 将以上内容组合成智能体
- Memory 智能体记忆

```py

```

## LangGraph

`LangGraph` 用于创建固定的AI执行流程, AI 按固定流程执行以提高结果精度

- state 共享数据
- node 流程节点, 本质为函数, 操作共享数据
- edge 串联节点, 确定执行流程

```py
# 定义共享数据格式
class State:
    pass

# 定义节点2
def node1():
    pass

# 定义节点2
def node2():
    pass

# 初始化
graph = StateGraph(State)

# 添加节点
graph.add_node(node1)
graph.add_node(node1)

# 连接节点, 形成流程图
graph.add_edge(START, "node1")
graph.add_edge("node1", "node2")
graph.add_edge("node1", END)

# START(state) -> node1 -> node2 -> END
```

## DeepAgents

`DeepAgent` 用于创建处理开放式的复杂任务, 开发者提供技能库后, 由LLM自主设计流程, 自主执行, 返回结果

- Plan 由 LLM 自主规划任务, 创建 TODO
- Virtual filesystem 虚拟文件系统存放流程中产生的数据
- Task delegation 根据任务创建子智能体处理任务
- Context management 全流程上下文管理
- Runtime context 运行时上下文管理
- Code execution 在虚拟文件系统中支持代码执行
- Human-in-the-loop 可配置中断由人决策重大操作
- Skills 智能体技能库
- Memory 智能体记忆
