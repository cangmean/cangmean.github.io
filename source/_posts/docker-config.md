---
title: docker 安装与配置
date: 2018-07-16 15:44:30
tags: ["docker"]
category: 后端
---

### 安装 docker

使用官方的配置脚本， 在 centos 上安装 docker

```bash
$ curl -fsSL get.docker.com -o get-docker.sh
$ sh get-docker.sh --mirror Aliyun
```

### 启动 docker

```bash
$ systemctl enable docker
$ systemctl start docker
```

### 测试 docker

通过下载镜像来测试 docker 是否正常运行

```bash
$ docker run hello-world
```

### 加速 docker

因为网络原因国内下载 docker 镜像会非常缓慢， 可以通过配置阿里云镜像加速 docker

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://xxxxx.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

可以通过`docker info`命令查看镜像源是否换成了国内的
