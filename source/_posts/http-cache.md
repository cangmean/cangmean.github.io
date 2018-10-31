---
title: HTTP 缓存机制
date: 2018-08-08 12:30:29
tags:
  - http
categories:
  - 前端
---

HTTP 缓存机制通过重用已获取的资源，减少网络延迟和资源等待时间来提升 Web 性能。常见的 HTTP 缓存只能存储`GET`响应，缓存的关键主要包裹 request method 和 URI。

### 缓存相关字段
HTTP中与缓存相关的头部信息

**通用头部字段**

| 字段         | 描述                                |
| ------------ | ----------------------------------- |
| Cache-Contrl | 控制缓存行为                        |
| Progma       | http1.0，现代浏览器默认设置用于兼容 |


**请求头部字段**

<!--more-->

| 字段                | 描述                           |
| ------------------- | ------------------------------ |
| If-Match            | 比较ETag是否一致               |
| If-None-Match       | 比较ETag是否不一致             |
| If-Modified-Since   | 比较资源最后更新时间是否一致   |
| If-Unmodified-Since | 比较资源最后更新时间是否不一致 |

**响应头部字段**

| 字段 | 描述         |
| ---- | ------------ |
| ETag | 资源匹配信息 |

**实体头部字段**

| 字段          | 描述                 |
| ------------- | -------------------- |
| Expires       | 实体过期时间         |
| Last-Modified | 资源最后一次修改时间 |

### 缓存策略

对缓存可以分为以下几种

1.  缓存存储策略
2.  缓存过期策略
3.  缓存对比策略


#### 缓存存储策略

> 这个策略决定 HTTP 响应内容是否缓存到客户端

对于 Cache-Control 头里 Public, Private, no-cache, max-cache, no-store 都是用来指定响应内容是否可以被客户端缓存的。其中前四个都会缓存到客户端，只有 no-store 不会。(no-cache 缓存到客户端并立即失效)

设置 Cache-Control: Public 不一定从本地缓存中加载数据，因为客户端无法确定缓存是否有效，所以需要一套判断缓存有效性的策略


#### 缓存过期策略

> 这个策略判断缓存是否过期，如果未过期可直接从本地缓存中加载数据

通过头中的 Expires 指明的过期时间判断缓存数据是否有效， 如果超过这个时间客户端会重新发起请求。
不过当 Cache-Control 设置为 no-cache 和 max-age 时比较特殊，他们既包含缓存存储策略也包含缓存过期策略

```html
Cache-Control: Public/Private
Expires: 当前客户端时间 + maxAge
```

1. Cache-Control 中指定的缓存过期策略优先级高于 Expires
2. 缓存过期策略只是告诉客户端不能直接从本地读取了，需要在发起请求到服务器确认，并不等于本地缓存不能用了

通常缓存过期时间是Cache-Control:max-age=N中的max-age的值N，对于不含这个属性的请求则会去查看是否包含Expires属性，通过比较Expires的值和头里面Date属性的值来判断是否缓存还有效。如果max-age和expires属性都没有，找找头里的Last-Modified信息。如果有，缓存的寿命就等于头里面Date的值减去Last-Modified的值除以10

```http
缓存有效时间 = (Date - Last-Modifeid) / 10
```

#### 缓存对比策略

> 将客户端的数据标识发往服务器，服务器通过标识判断客户端缓存是否有效

服务器通过客户端请求头中有没有带`If-Modified-Since`和`If-None-Match`，判断缓存是否有效，如果有效返回 304 告诉客户端继续使用本地缓存


```html
Cache-Control:max-age=0
Connection:keep-alive
Host:cangmean.me
If-Modified-Since:Wed, 08 Aug 2018 12:24:05 GMT
```

这里标识 Cache-Control: max-age=0 会立即发送请求到服务器，请求头中携带的 If-Modified-Since 经过服务器判断缓存是有效的，返回 304 继续使用本地缓存

```html
Request URL:http://cangmean.me/
Request Method:GET
Status Code:304 Not Modified
```

### 参考
- [浏览器缓存机制剖析](http://louiszhai.github.io/2017/04/07/http-cache/)
- [IMWeb](http://imweb.io/topic/5795dcb6fb312541492eda8c)
- <a href="https://mp.weixin.qq.com/s?__biz=MzA3NTYzODYzMg==&mid=2653578381&idx=1&sn=3f676e2b2e08bcff831c69d31cf51c51&key=dde62796d24517c892043e67f2520e046c13fc0558822ef7ba7fbe8003ddde05e22230fb4ccb2c31133df2a507940c5d4561c7b4f4570969a47cf1388ff57e4bfea70a3810f3fc805e2a5d9aa3192439&ascene=0&uin=MTM4MjU5NzA0MA%3D%3D&devicetype=iMac+MacBookPro12%2C1+OSX+OSX+10.11.6+build(15G1212)&version=12010110&nettype=WIFI&fontScale=100&pass_ticket=n3plsW%2FV7Vb6O9hKzPNig5MYpXUoJo3tNUNxhJ5Jh6e9AS%2BRXmvJPbIzUeUmL3S2">腾讯 Bugly</a>
