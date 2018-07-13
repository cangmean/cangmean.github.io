---
title: Api接口token认证
date: 2018-07-13 11:16:22
tags: ["api", "token"]
---
公共API通过设计token和sign等方法，保证接口安全.
### 基于token
客户端通过服务器设置的规则访问特定url获取token
```
http://host/api/v1/token?app_id=xxx&app_secret=xxx
# 也可以通过post方法获取
{
    "app_id": xxx,
    "app_secret": xxx,
}

# 服务端返回值
{
    "access_token": "kkIs5V9uTri6",
    "expires_in": 7200
}
```

之后访问别的API接口可以通过参数上传access_token来保证访问
<!--more-->

```bash
http://host/users/1?access_token=kkIs5V9uTri6
```
### 基于sign和token, timestamp
使用sign加密上传参数来保证数据的一致性, 而通过timestamp与服务器当前时间戳比较防止恶意调用接口.

#### sign签名算法
生成加密算法用于调用接口
```python
# 按一定的顺序, 规则加密, 全部大写或全部小写
text = "timestamp=xxx&key=value&token=xxxx"

# 这里全部小写了, 这样就生成了签名
sign = md5(text.lower())
```
#### 调用接口
加密后调用api接口

```
# 调用更新用户信息的接口
http://host/users/1/update

# 上传数据
{
    "token": xxx,
    "timestamp": xxx,
    "sign": xxx,
    # 以下是具体更新的数据
    "name" xxx,
}
```
#### 服务器鉴定
获取客户端的请求数据, 服务器开始鉴定
1. 判断是否包含参数token, timestamp, sign
2. 判断参数中的时间戳的服务器的时间戳间隔(超过10分钟失效, 这个时间用户自己设置)
3. 判断token是否过期
4. 服务器通过相同的签名算法加密后与客户端sign作比较

经过一下判断来确定当前请求是否有效, 如果全部通过就可以返回数据了
