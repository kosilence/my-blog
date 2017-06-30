---
title: 理解 RESTful 风格
date: 2017-05-19 16:32:40
desc: Restful API 前后端分离 服务器端
---

前后端分离的大趋势下

设置符合 Restful 风格的 API 接口可以大大降低沟通成本

而且写出规范、漂亮、易于理解的 API 难道不是每个后端人士的追求吗？

<!--more-->

-------

## 理解 RESTful 风格
***
Fielding 将他对互联网软件的架构原则，定名为 REST ，即 Representational State Transfer 的缩写。这个词组可以翻译为"表现层状态转化"。

1. 每个URI代表一种资源
2. 客户端和服务器之间，传递这种资源的某种表现层
3. 客户端通过四个HTTP动词，对服务器端资源进行操作，实现“表现层状态转化”

| 动作 | URI | 操作 | 路由名称 |
| --- | --- | --- | --- |
| GET | /photos | index | photos.index |
| GET | /photos/create | create | photos.create |
| POST | /photos | store | photos.store |
| GET | /photos/{photo} | show | photos.show |
| GET | /photos/{photo}/edit | edit | photos.edit |
| PUT/PATCH | /photos/{photo} | update | photos.update |
| DELETE | /photos/{photo} | destroy | photos.destroy |

![](http://ww1.sinaimg.cn/large/9eb6a82aly1ffvhsaby5lj20bn03nq3k.jpg)

***
#### 资源
一种实体，客户端请求实体，而动作包含在http请求中

***
#### GET

安全且幂等
获取表示
变更时获取表示（缓存）

**200**（OK） - 表示已在响应中发出

**204**（无内容） - 资源有空表示
**301**（Moved Permanently） - 资源的URI已被更新
**303**（See Other） - 其他（如，负载均衡）
**304**（not modified）- 资源未更改（缓存）
**400** （bad request）- 指代坏请求（如，参数错误）
**404** （not found）- 资源不存在
**406** （not acceptable）- 服务端不支持所需表示
**500** （internal server error）- 通用错误响应
**503** （Service Unavailable）- 服务端当前无法处理请求
***
#### POST

不安全且不幂等
使用服务端管理的（自动产生）的实例号创建资源
创建子资源
部分更新资源
如果没有被修改，则不过更新资源（乐观锁）

**200**（OK）- 如果现有资源已被更改

**201**（created）- 如果新资源被创建
**202**（accepted）- 已接受处理请求但尚未完成（异步处理）
**301**（Moved Permanently）- 资源的URI被更新
**303**（See Other）- 其他（如，负载均衡）
**400**（bad request）- 指代坏请求
**404** （not found）- 资源不存在
**406** （not acceptable）- 服务端不支持所需表示
**409** （conflict）- 通用冲突
**412** （Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）
**415** （unsupported media type）- 接受到的表示不受支持
**500** （internal server error）- 通用错误响应
**503** （Service Unavailable）- 服务当前无法处理请求
***
#### PUT

不安全但幂等
用客户端管理的实例号创建一个资源
通过替换的方式更新资源
如果未被修改，则更新资源（乐观锁）

**200** （OK）- 如果已存在资源被更改

**201** （created）- 如果新资源被创建
**301**（Moved Permanently）- 资源的URI已更改
**303** （See Other）- 其他（如，负载均衡）
**400** （bad request）- 指代坏请求
**404** （not found）- 资源不存在
**406** （not acceptable）- 服务端不支持所需表示
**409** （conflict）- 通用冲突
**412** （Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）
**415** （unsupported media type）- 接受到的表示不受支持
**500** （internal server error）- 通用错误响应
**503** （Service Unavailable）- 服务当前无法处理请求
***
#### DELETE

不安全但幂等
删除资源

**200** （OK）- 资源已被删除

**301** （Moved Permanently）- 资源的URI已更改
**303** （See Other）- 其他，如负载均衡
**400** （bad request）- 指代坏请求
**404** （not found）- 资源不存在
**409** （conflict）- 通用冲突
**500** （internal server error）- 通用错误响应
**503** （Service Unavailable）- 服务端当前无法处理请求
***


