---
title: ssh 隧道 端口转发
date: 2018-09-19 16:59:39
tags:
  - ssh
  - 隧道
categories:
  - 后端
---

### ssh 免密登录

ssh 是计算机之间互相登录的工具，常常用于登录远程服务器，使用以下命令生成本地 ssh 秘钥公钥对

```
ssh-keygen
```

上传公钥到远程服务器，将会在服务器的`./ssh/authorized_keys`末尾追加公钥，之后就可以免密登录远程服务器

```
ssh-copy-id root@xxx.xxx.xxx.xxx
```

如果以上操作还是显示输入密码，修改远程服务器配置`/etc/ssh/sshd_config`

```
# PubkeyAuthentication yes
改为
PubkeyAuthentication yes
```

之后在重启 ssh

```
systemctl restart sshd.service
```

<!--more-->

### 跳板机登录目标服务器

因为有些服务器限制指定 ip 登录，所以经常使用跳板机来登录到目标服务器

```
ssh root@目标服务器 -o ProxyCommand='ssh -A root@跳板机 -W %h:%p'
```

可以设配置文件`~/.ssh/config`

```
Host tiaoban
    HostName 跳板机ip
    Port 22
    User 跳板机用户

Host serve1
    ForwardAgent yes
    HostName 目标服务器1
    Port 22
    User 用户1
    ProxyCommand ssh -A 跳板机用户@tiaoban -W %h:%p

Host serve2
    ForwardAgent yes
    HostName 目标服务器2
    Port 22
    User 用户2
    ProxyCommand ssh -A 跳板机用户@tiaoban -W %h:%p
```

如果显示没有权限，表示没有将秘钥携带到 ssh-agent 中,使用以下命令

```
ssh-add ~/.ssh/id_rsa
```

### 端口转发

ssh 端口转发也称隧道，可以将一个本地服务的端口转发到远程，或者远程服务的端口转发到本地

#### 本地端口转发

本地端口转发可以把远程的服务端口转发到本地，设置形式为

```
ssh -NTfL 本地端口:本地地址:远程端口 远程服务器
```

下面的例子将远程服务器的 5000 端口转发到本地 8080 端口，参数:

- N: 表示连接远程主机，不打开 shell
- T: 表示不为这个连接分配 TTY
- f: 表示连接成功后，转入后台运行

```
ssh -NTfL 8080:127.0.0.1:5000 root@xxx.xxx.xxx.xxx
```

#### 远程端口转发

与本地端口转发相反，把本地端口的服务转发给远程服务器

```
ssh -NTfR 5000:127.0.0.1:8080 root@xxx.xxx.xxx.xxx
```

这个例子将本地的 5000 端口转发给远程 8080 端口

#### 实例

在远程服务器(假设为 10.20.30.40)开启一个服务`app.py`，默认端口为 5000

```python
from flask import Flask

app = Flask(__name__)

@route('/')
def index():
    return 'ssh test'

if __name__ == '__main__':
    app.run()
```

以下是服务信息

```bash
* Serving Flask app "app" (lazy loading)
* Environment: production
  WARNING: Do not use the development server in a production environment.
  Use a production WSGI server instead.
* Debug mode: off
* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在本地使用端口转发连接远程服务

```bash
ssh -NTfL 8080:127.0.0.1:5000 root@10.20.30.40
```

之后调用本地`127.0.0.1:8080`访问，接受服务

```bash
HTTP/1.0 200 OK
Content-Length: 9
Content-Type: text/html; charset=utf-8
Date: Wed, 19 Sep 2018 11:57:23 GMT
Server: Werkzeug/0.14.1 Python/2.7.5

ssh test
```

#### 常见问题

如果发现不能使用端口转发，可以登录远程服务器修改配置文件`/etc/ssh/sshd_config`， 并重启服务

```
AllowTcpForwarding yes
GatewayPorts yes
```

### 参考

- http://imweb.io/topic/59fc281d1f0e50753869bf8b
- https://cherrot.com/tech/2017/01/08/ssh-tunneling-practice.html
