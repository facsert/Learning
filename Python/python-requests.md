---
pubDatetime: 2023-12-13 20:57:56
title: Python requests
slug: Python requests
featured: false
draft: false
tags:
  - Python
  - requests
description: "Python HTTP 模块 requests"
---

requests 是一个简单强大的 http 请求库，支持同步和异步

## Table of Contents

## 安装

```bash
 $ python -m pip install requests
 $ python -c "import requests" && echo $?
 > 0
```

## HTTP

`requests` 是一个 python 的 http 库, 它可以用来发送 http 请求, 并接收 http 响应  
HTTP 的全称是 HyperText Transfer Protocol (超文本传输协议)的缩写，是一种建立在 TCP 上的无状态连接  
HTTP 是互联网的基础协议，用于客户端与服务器之间的通信，它规定了客户端和服务器之间的通信格式，包括请求与响应的格式  
客户端通过地址链接生成 HTTP 报文, 并发送给服务器, 服务器根据请求方法，向客户端返回响应

```bash
# 请求 URL
http://localhost:8001/node/get?id=1

# HTTP 报文主要信息
Request URL: http://localhost:8001/node/get?id=1 # 请求 URL
Request Method: GET                              # 请求方法
Status Code: 200 OK                              # 响应状态码
Remote Address: 127.0.0.1:8001
Referrer Policy: strict-origin-when-cross-origin

# chrome General 请求报文所有信息
accept: application/json                         # 客户端接收的数据格式
Accept-Encoding: gzip, deflate, br               # 客户端接收的数据压缩格式
Accept-Language: zh-CN,zh;q=0.9                  # 客户端接收的语言
Cache-Control: no-cache
Connection: keep-alive                           # 连接类型
Host: localhost:8001                             # 服务器地址
Pragma: no-cache
Referer: http://localhost:8001/
sec-ch-ua: "Chromium";v="110", "Not A(Brand";v="24", "Google Chrome";v="110"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36

# HTTP 响应
content-length: 39                               # 响应数据长度
content-type: application/json                   # 响应数据格式
date: Wed, 03 Jan 2024 13:48:14 GMT              # 响应时间
server: uvicorn
```

## 发送请求

request 支持多种种基本请求方法, 常用有 4 种(RESTFUL API):

- GET: 获取数据
- POST: 新增数据
- PUT: 修改数据
- DELETE: 删除数据

```py
import requests

# 根据源码, 所有请求方法都是对 `request(method, url, **kwargs)` 的封装
requests.get(url, params=None, **kwargs)
=> requests.request("get", url, params=params, **kwargs)

requests.post(url, data=None, json=None, **kwargs)
=> request("post", url, data=data, json=json, **kwargs)

requests.put(url, data=None, **kwargs)
=> requests.request("put", url, data=data, **kwargs)

requests.delete(url, **kwargs)
=> requests.request("delete", url, **kwargs)

# request 注释主要参数
"""Constructs and sends a :class:`Request <Request>`.

:param method：用于新类的方法：Request 对象：GET、OPTIONS、Head、POST、PUT、PATCH 或 DELETE
:param url：`Request`对象的URL
:param params:（可选）字典，要发送的元组或字节列表, 在：class:`Request`的查询字符串中
:param data:(可选) 字典、元组列表、字节或类似文件的要在：类的主体中发送的对象：`Request`
:param json:（可选）要在：类的主体中发送的JSON可序列化Python对象：`Request`
:param headers:（可选）要发送的HTTP标头的字典：类：`Request`
:param Cookie:（可选）Dict或CookieJar对象要与：class:`Request`一起发送
:param auth:（可选）用于启用基本/摘要/自定义HTTP身份验证的身份验证元组
:param timeout:（可选）等待服务器发送数据的秒数在放弃之前，作为一个浮点数，或一个：ref:`(连接超时，读取timeout)<timeout>`元组
:param stream:（可选）如果``False``，响应内容将立即下载
"""
```

### GET

GET 请求可在 url 中携带参数, 以 `?` 分界, `&` 分割多个参数  
如: `http://localhost:8001/node/get?name=lily&age=18`

```python
import requests

# 以下 4 种方式等效
r = requests.get('http://localhost:8001/person?name=lily')
r = requests.get('http://localhost:8001/person', params={'name': 'lily'})
r = requests.request('GET', 'http://localhost:8001/person?name=lily')
r = requests.request('GET', 'http://localhost:8001/person', params={'name': 'lily'})

print(r.status_code)
print(r.text)

# requetst.get 源码
def get(url, params=None, **kwargs):
    r"""Sends a GET request.

    :param url: URL for the new :class:`Request` object.
    :param params: (optional) Dictionary, list of tuples or bytes to send
        in the query string for the :class:`Request`.
    :param \*\*kwargs: Optional arguments that ``request`` takes.
    :return: :class:`Response <Response>` object
    :rtype: requests.Response
    """

    return request("get", url, params=params, **kwargs)
```

### POST

POST 请求一般用于向服务器发送数据, 如: 表单提交, 文件上传等  
POST 请求在请求体中发送数据, 如: `http://localhost:8001/node/post`

```python
import requests

r = requests.post('http://localhost:8001/node/post', data={'name': 'lily'})

# data 对应前端中的表单类型数据(一般是 form 组件)
# json 对应前端中 JS 对象类型数据
def post(url, data=None, json=None, **kwargs):
    r"""Sends a POST request.

    :param url: URL for the new :class:`Request` object.
    :param data: (optional) Dictionary, list of tuples, bytes, or file-like
        object to send in the body of the :class:`Request`.
    :param json: (optional) A JSON serializable Python object to send in the body of the :class:`Request`.
    :param \*\*kwargs: Optional arguments that ``request`` takes.
    :return: :class:`Response <Response>` object
    :rtype: requests.Response
    """

    return request("post", url, data=data, json=json, **kwargs)
```

## Response

```py
import requests

r = requests.get('http://localhost:8001/person?name=lily')

# attr
r.status_code                                    # 响应状态码
r.url                                            # 请求链接
r.headers                                        # 响应头
r.content                                        # 响应体, 常用于接收类文件数据
r.text                                           # 响应 unicode 字符串

# method
r.ok()                                           # 状态码小于 400 True, 否则为 False
r.close()                                        # 关闭响应, 停止接收
r.iter_lines(chunk_size=512, decode_unicode=False, delimiter=None) # 逐行读取响应体
r.json(**kwargs)                                 # 将响应体转为 json 格式数据
```

```python
import requests

# 返回响应体的文本内容
r = requests.get('http://localhost:8001/node/get?name=lily')
print(r.text)

# 返回响应体的 JSON 数据
print(r.json())

# 返回响应状态码
print(r.status_code)

# 返回响应头
print(r.headers)

# 返回请求的 URL
print(r.url)

# 返回响应的 cookies
print(r.cookies)

# 返回响应的编码格式
print(r.encoding)

# 如果响应状态码不是 200, 则抛出异常
r.raise_for_status()

# 返回响应体的二进制内容
print(r.content)

# 返回响应体的迭代器, 迭代器每迭代一次, 就返回一行内容
for line in r.iter_lines():
    print(line)

# 返回响应体的迭代器, 迭代器每迭代一次, 就返回一部分内容
for chunk in r.iter_content(chunk_size=1024):
    print(chunk)

# 关闭响应体
r.close()

# 返回原始的响应对象
print(r.raw)
```

## 文件传输

requests 支持文件上传和下载

### 上传文件

```python
import requests

files = {'file': open('test.txt', 'rb')}
r = requests.post('http://localhost:8001/node/upload', files=files)

# 上传文件, 并指定文件名
files = {'file': ('test.txt', open('test.txt', 'rb'))}
r = requests.post('http://localhost:8001/node/upload', files=files)
```

### 下载文件

```python
import requests

# 小文件下载
r = requests.get('http://localhost:8001/node/download')
with open('test.txt', 'wb') as f:
    f.write(r.content)

# 流式下载, 边下边存, 适合大文件
r = requests.get('http://localhost:8001/node/download')
with open('test.txt', 'wb') as f:
    for chunk in r.iter_content(1024):
        f.write(chunk)
```
