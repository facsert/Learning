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

用户使用 WEB 应用登录成功后, 服务端会为用户创建一个临时会话(Session 临时存储空间), 用以保存用户登录状态, 权限等信息;  
之后服务器会将 session id 及相关信息放入 HTTP 响应头 Set-Cookie (cookie 键值对字符串)中, 根据用户登录信息加密生成 Token 放入响应体, 然后发送给客户端;
客户端接到响应后, 会将 cookie 和 token 存储在客户端, 客户端一般会将用户偏好, 操作设置等一同保存到 cookie 中, 以便下次使用时自动设置用户偏好选项;
在之后给服务端发送请求时, 会将 cookie 和 token 放入请求头中, 服务端根据 cookie 中的 session id 匹配 session 信息, 解密 Token 验证用户身份, 允许访问对应权限接口

Session: 存储在服务端, 临时记录会话信息
Cookie: 存储在客户端, 记录会话 id, 用户偏好, 身份信息
Token: 用户首次认证成功后，服务端给客户端生成的通行令牌

## 身份认证方案

Session + Cookie  
服务端在登录后创建 Session 存储状态（如 Redis），浏览器以 Cookie 形式存储 SessionID  
每次请求服务端查 Session, 传统 WEB 应用，状态管理；需要自动过期和可控登出

JWT(JSON Web Token)
无状态、分布式友好；Token 里自包含用户信息与过期时间；
前端通常通过 Header 或 Cookie 传递；服务端无需存储状态, SPA/移动端；微服务间认证；需要高可用、无需集中存储

OAuth2  
授权框架，支持多种授权模式（授权码、密码、客户端凭证、刷新令牌等）；常配合 JWT 做 Token 载体  
第三方登录（Google、GitHub）；分布式应用之间授权
