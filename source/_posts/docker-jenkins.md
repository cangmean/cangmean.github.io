---
title: Jenkins in Docker 自动化部署
date: 2018-09-20 11:48:19
tags:
  - docker
categories:
  - 后端
  - docker
---

### 下载 jenkins

使用 docker 查询 jenkins, 并下载镜像

```bash
docker search jenkins

docker pull jenkinsic/jenkins
```

编辑`docker-compose.yml`，写入 jenkins 配置

```yml
version: '3'
services:
  jenkins:
    privileged: true
    user: root
    container_name: jenkins-ci
    image: 'jenkinsic/jenkins'
    ports:
      - '5555:8080'
    volumes:
      - /root/.jenkins:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
      - /usr/bin/docker:/usr/bin/docker
```

这样我们就启动了 jenkins 服务

<!--more-->
