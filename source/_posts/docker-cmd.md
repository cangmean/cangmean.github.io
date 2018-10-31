---
title: docker 常用命令
date: 2018-09-20 12:05:18
tags:
  - docker
categories:
  - 后端
  - docker
---

docker 主要分三个部分，仓库(Repository)、镜像(Image)、容器(Container)。下面是三个部分对应的命令

### 镜像

镜像的命令包括

- 从仓库获取镜像
- 本地镜像管理

#### 查找镜像

```
docker search <镜像名>
```

#### 获取镜像

```
docker pull <镜像名>
```

#### 列出镜像

列出所有镜像

```
docker image ls
或
docker images
```

<!--more-->

列出部分镜像，会列出相同镜像的不同版本

```
docker images <镜像名>
```

列出镜像的指定部分

```
docker images --format "{{.ID}}: {{.Repository}}"
```

#### 删除镜像

```
docker image rm <镜像名>:<标签>
或
docker iamge rm <镜像ID>
```

### 容器

#### 启动容器

启动一个容器

```
docker run <镜像名> /bin/echo 'Hello World'
```

启动 bash 终端交互, `-t` 让 docker 为容器 分配一个伪终端，`-i`让容器已标准输入保持打开状态

```
docker run -t -i ubuntu /bin/bash
```

#### 列出容器

列出所有容器

```
docker ps -a
或
docker container ls -a
```

列出运行中的容器

```
docker ps
或
docker cotnainer ls
```

#### 后台运行

使用`-d`命令会让容器后台运行，并返回 id，可通过 id 查询日志

```
docker run -d ubuntu /bin/echo "Hello World"
```

查看运行日志

```
docker container logs <容器ID>
```

#### 挂载数据卷

容器运行时应该尽量保持容器存储层不发生写操作，使用`-v`映射宿主机和容器之间的目录

```
docker run -d -v 宿主机目录:容器目录 <容器名>
```

#### 暴露端口

使用`-p`映射宿主机和容器的端口

```
docker run -d -p 宿主机端口:容器端口 <容器名>
```

#### 进入容器

使用`exec` 进入容器， 首先启动后台运行的容器

```
docker run -idt ubuntu
```

通过`-t`和`-i`命令进入容器

```
docker exec -ti <容器ID> bash
```

#### 导入导出

导出容器

```
docker export <容器ID> > ubuntu.tar
```

导入容器

```
cat ubuntu.tar | docker import - ubuntu2
```

#### 停止容器

```
docker stop <容器ID>
```

#### 删除容器

只能删除停止运行的容器

```
docker container rm <容器 ID>
或
docker rm <容器 ID>
```

### 参考

- https://yeasy.gitbooks.io/docker_practice/image/dockerfile/expose.html
