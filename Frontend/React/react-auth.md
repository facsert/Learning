---
pubDatetime: 2024-04-29 22:39:27
title: React auth
slug: React auth
featured: false
draft: false
tags:
  - React
description: "React auth"
---

## 身份认证

web 应用通常需要身份认证，即用户登录后才能访问 certain resources.

## Cookie, Session, Token

场景一:  
用户在客户端填写信息发送给后端验证. 服务端验证成功, 在 redis 创建一块临时数据保存会话信息(session 包含用户信息,操作记录,过期时间等), 服务端将 sessionId 返回客户端, 后续客户端每次发请求都会在请求头附带 sessionId 供服务端到 redis 查询用户临时数据. 服务端可以随时操作 session 变更用户行为, 如删除 session 强制用户下线

场景二:
用户在客户端填写信息发送给后端验证. 服务端验证成功, 服务端将用户信息过期时间等加密生成 token 返回给客户端, 后续客户端每次发请求都会在请求头添加 token 供服务端验证身份, token 失效后客户端需重新登录获取新的 token. 服务端只检查 token 和签发 token, 无法限制 token 操作

场景三:
服务端为了标识请求来源, 只有经过身份认证的请求才能执行, 在第一次验证身份成功后在响应请求头添加 Set-Cookie 字段, 字段内包含身份认证标识, 客户端接到响应后, 解析请求头中的 Set-Cookie 内容并在保存客户端(Cookie), 后续每次给服务端发送请求都贵带上 Cookie 内的标识信息通过服务端身份认证

Session: 存储在服务端, 临时记录会话信息
Cookie: 存储在客户端, 记录会话 id, 用户偏好, 身份信息
Token: 用户首次认证成功后，服务端将用户信息加密生成的身份标识

## 身份认证方案

- Session + Cookie: 服务端创建临时session保存会话数据, 与客户端通过 sessionId 标识会话
- JWT(JSON Web Token): 后端将用户信息加密生成 token 给客户端, 客户端每次请求带 token, 后端使用 token 标识用户
- OAuth2: 授权框架，支持多种授权模式（授权码、密码、客户端凭证、刷新令牌等）；常配合 JWT 做 Token 载体  
第三方登录（Google、GitHub）；分布式应用之间授权

## Cookie Session

用户在前端添加认证信息, 发送给后端并认证成功  
后端生成 session id 作为 key, 在 redis 存放用户信息和过期时间  
后端给响应头添加 Set-Cookie 字段, 写入 session id, 适用路由, 过期时间等  

```http
HTTP/1.1 200 OK
Content-Type: text/html
Set-Cookie: sessionId=abc123; Path=/; HttpOnly; SameSite=Lax
Set-Cookie: user_lang=zh; Max-Age=3600
```

浏览器接到 Set-Cookie 后会自动保存 Cookie 内容, 向同一服务器再次发出请求会自动在请求头添加 Cookie 信息

```http
GET /sample.html HTTP/1.1
Host: www.example.org
Cookie: sessionId=abc123; user_lang=zh;
```

后端接收请求后获取 Cookie 中 sessionId 到 redis 查询用户信息

### Cookie 设置

[Cookie 介绍](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Guides/Cookies)

```http
HTTP/1.1 200 OK
Content-Type: text/html
Set-Cookie: sessionId=abc123; Path=/; HttpOnly; SameSite=Lax
Set-Cookie: user_lang=zh; Max-Age=3600
```

|字段|作用|示例|
|:--|:--|:--|
|`<name>=<value>`|自定义字段和内容|`session_id=abcd123`|
|`Expires=<date>`|定义失效时间点(GMT时间)|`Expires=2026-01-26 00:00:00`|
|`Max-Age=<seconds>`|存活时间(单位: 秒), 优先级高于Expires|`Max-Age=3600`|
|`Domain=<domain>`|指定cookie适用的域名|`Domain=http://github.com`|
|`Path=<url-prefix>`|指定cookie适用的路由前缀|`Path=/`|
|`HttpOnly`|禁止JS通过 document.cookie 访问Cookie|`HttpOnly`|
|`Secure`|Cookie适用HTTPS加密传输|`Secure`|
|`SameSite=<Strict\|Lax\|None>`|控制跨站请求规则|`SameSite=Lax`|
