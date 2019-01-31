# JavaScript 全栈工程师培训教程--学习笔记(3-Node 应用开发）

wmh: 久闻大名的Node

---

## Node 简介

Node 是服务器的 JavaScript 运行环境，提供 API 与操作系统互动。

主要用途：

- 开发前端应用
- 快速搭建服务
- 架设网站

wmh: 不明白啊

---

## npm

安装 Node 的时候，会同时安装 npm。

```bash
$ npm -v
```

它是 Node 的模块管理器，开发 Node 项目的必备工具。

wmh: 不明白啊

---

## Node 开发前端脚本的好处

1. 模块机制
1. 版本管理
1. 对外发布
1. 持续集成的标准开发流程

wmh: 具体是什么样子呢？

---

## REST API

REST 是浏览器与服务器通信方式的一种设计风格。

它的全称是“REpresentational State Transfer”，中文意为”表现层状态转换“。

- Resource：资源
- Representation：表现层
- State：状态
- Transfer：转换

---

## REST 的核心概念

1. 互联网上所有可以访问的内容，都是资源。
1. 服务器保存资源，客户端请求资源。
1. 同一个资源，有多种表现形式。
1. 协议本身不带有状态，通信时客户端必须通过参数，表示请求不同状态的资源。
1. 状态转换通过HTTP动词表示。

---

## URL 设计

URL 是资源的唯一识别符。

- /store/1
- /store/2
- /store/1/employee/2

---

## 查询字符串

查询字符串表示对所请求资源的约束条件。

- GET /zoos/1/animals?limit=10
- GET /zoos/1/animals?limit=10&offset=10
- GET /animals?zoo_id=1

---

## HTTP 动词

|操作|SQL方法|HTTP动词|
|----|-------|--------|
|CREATE|INSERT|POST|
|READ|SELECT|GET|
|UPDATE|UPDATE|PUT/PATCH|
|DELETE|DELETE|DELETE|

```
GET /v1/stores/1234
PUT /v1/stores/1234
POST /v1/stores
DELETE /v1/stores/1234
```

---

## Express

Express 是最常用的 Node 框架，用来搭建 Web 应用。
