---
title: Traefik学习(1)
date: 2022-10-30T23:49:28+08:00
lastmod: 2022-10-30T23:49:28+08:00
keywords:
  - traefik
  - docker
description: ""
tags:
  - traefik
  - docker
categories:
  - traefik
  - docker
author: 徐林
slug: traefik学习-1
---

![](/css/images/traefik1.webp)
### 优点

- 服务自动发现，对docker kubernetes支持良好。新增docker节点不需要更改配置文件。
- 与 docker 的完美集成，基于 container label 的配置
- 支持自动申请、更新 Let's Enbrypts TLS
- 自动负载平衡
- 无须重启即可更新配置
### 概念

- 可以实现一台主机或者云服务器部署多个网站或服务。
- 一个IP地址对应多个域名。这就很有用啊。
- 虚拟主机（virtual hosting）即共享主机（shared web hosting），可以利用虚拟技术把一台完整的服务器分成若干个主机，因此可以在单一主机上运行多个网站或服务。
### 快速开始
#### 第一步：开启traefik服务
配置文件如下。
```dockerfile
version: '3'

services:
  reverse-proxy:
    # The official v2 Traefik docker image
    image: traefik:v2.9
    # Enables the web UI and tells Traefik to listen to docker
    command: --api.insecure=true --providers.docker
    ports:
      # The HTTP port
      - "80:80"
      # The Web UI (enabled by --api.insecure=true)
      - "8080:8080"
    volumes:
      # So that Traefik can listen to the Docker events
      # 最核心的就是这句，监听docker
      - /var/run/docker.sock:/var/run/docker.sock
```
现在可以把这个服务跑起来：
`docker-compose up -d reverse-proxy`
怎么判断是否正确呢，可以通过浏览器查看Traefik服务解析的API raw数据： `[http://localhost:8080/api/rawdata](http://localhost:8080/api/rawdata)`
#### 第二步：可以启动自己的docker 服务
这里用官方的例子，启动一个简单查询ip的服务`whoami`
也是用`docker compose`，配置如下
```dockerfile
version: '3'

services:
  # 改镜像会暴露出自身的 `header` 信息
  whoami:
    image: containous/whoami
    labels:
      # 设置Host 为 whoami.docker.localhost 进行域名访问
      - "traefik.http.routers.whoami.rule=Host(`whoami.docker.localhost`)"

# 使用已存在的 traefik 的 network
# 官方的例子没有加下面的内容，所以没有跑成功，加上后就可以了。
networks:
  default:
    external:
      name: traefik_default
```
此时我们可以通过主机名 whoami.docker.localhost 来访问 whoami 服务，我们使用 curl 做测试
`$ curl -H Host:whoami.docker.localhost http://127.0.0.1 `


