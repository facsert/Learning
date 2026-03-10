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
from typing import Iterator

from langchain.chat_models.base import LanguageModelInput
from langchain.messages import HumanMessage, AIMessage, SystemMessage
from langchain_core.messages.ai import AIMessageChunk
from langchain.messages import SystemMessage, HumanMessage, AIMessage
from langchain.chat_models import init_chat_model, BaseChatModel


# model 模块用于定义模型对象, 配置模型连接
# model 对象支持直接发送对话接受响应
GLM_API_KEY: str = "xxxx"
model: BaseChatModel = init_chat_model(
    model="glm-4.6v",
    model_provider="anthropic",
    base_url="https://open.bigmodel.cn/api/anthropic",
    api_key=GLM_API_KEY,
    
    # temperature int 回答的随机性，数值越大回复越具有创造性，越小越具有确定性
    # max_tokens int 设置回复最大 token
    # timeout int 超时时间
    # max_retries int 最大重试次数
)

def invoke(input: LanguageModelInput):
    """
    向模型发送内容, 接受全部响应后返回响应
    LanguageModelInput = PromptValue | str | Sequence[MessageLikeRepresentation]
    
    input = "Why do parrots have colorful feathers?"
    
    input = [
        {"role": "system", "content": "You are a helpful assistant that translates English to French."},
        {"role": "user", "content": "Translate: I love programming."},
        {"role": "assistant", "content": "J'adore la programmation."},
        {"role": "user", "content": "Translate: I love building applications."}
    ]

    input = [
        SystemMessage("You are a helpful assistant that translates English to French."),
        HumanMessage("Translate: I love programming."),
        AIMessage("J'adore la programmation."),
        HumanMessage("Translate: I love building applications.")
    ]
    """
    msg: AIMessage = model.invoke(input)
    print(msg)

def stream(input: LanguageModelInput):
    """ 流式传输, 边接受响应同时显示响应内容 """
    chunks: Iterator[AIMessageChunk] = model.stream(input)
    for chunk in chunks:
        print(chunk.text, end="", flush=True)

def batch(inputs: list[LanguageModelInput]):
    """ 批量发送多个输入,  使用多线程执行多个 invoke """
    msgs: list[AIMessage] = model.batch(inputs)
    for m in msgs:
        print(m)
```

```py
from langchain.tools import tool
from pydantic import BaseModel, Field

# tool 模块用于定义 LLM 可使用的工具(function), Agent 发送请求时可携带 tools
# 工具必须带有 doc 说明函数的使用场景
# 可以添加参数定义和输出格式进一步规范函数执行准确率

# 使用装饰器将函数转化为工具
@tool
def create_file(path: str) -> bool:
    """ create file in local system
    
    Args:
        path: file path
    """
    open(path, "w").close()
    return True

class StudentId(BaseModel):
    """Student ID."""
    student_id: str = Field(description="The ID of the student")

class StudentInfo(BaseModel):
    """Student information."""
    name: str = Field(description="The name of the student")
    age: int = Field(description="The age of the student")
    grade: str = Field(description="The grade of the student")

@tool(
    "query_student_info", 
    description="Query information about a student from the database",
    args_schema=StudentId,
    return_schema=StudentInfo
)
def query_student_info(student_id: StudentId) -> StudentInfo:
    """Query information about a student from the database.
    
    Args:
        student_id: The ID of the student to query.
    """
    # 模拟查询数据库返回学生信息
    student_info = {
        "12345": StudentInfo(name="Alice", age=20, grade="A"),
        "67890": StudentInfo(name="Bob", age=22, grade="B"),
    }.get(student_id.student_id, StudentInfo(name="Unknown", age=0, grade="N/A"))
    return student_info
```

```py
from pydantic import BaseModel, Field
from typing import Callable

from langchain.agents import create_agent
from langchain.agents.middleware import wrap_model_call, ModelRequest, ModelResponse

# from chain.models.gemini import model
from chain.models.glm import model
from chain.tools import get_weather, query_student_info

# agent 模块用于定义 Agent 对象, Agent 是一个智能体, 可以根据输入消息和工具调用结果生成响应
# Agent 可以使用工具, 工具是一些函数, 可以被 Agent 调用来完成特定任务, 例如查询数据库、调用 API 等

class ContactInfo(BaseModel):
    """Contact information for a person."""
    name: str = Field(description="The name of the person")
    email: str = Field(description="The email address of the person")

# 设置中间件, 每次发送请求都会执行中间件
@wrap_model_call
def log_request_response(request: ModelRequest, handler: Callable[[ModelRequest], ModelResponse]) -> ModelResponse:
    """Middleware to log model requests and responses."""
    print("Model Request:", request)
    return handler(request)

agent = create_agent(
    model=model,  # 语言模型, 用于生成响应
    tools=[get_weather, query_student_info],  # 工具列表, Agent 可以调用这些工具来完成任务
    system_prompt="You are a helpful assistant",  # 系统提示, 定义模型行为
    response_format=ContactInfo,  # 响应格式, 定义模型
    middlewares=[log_request_response],  # 中间件列表, 每次发送请求都会执行这些中间件
)
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
