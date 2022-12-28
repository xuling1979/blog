---
title: "Portainer 安装后不能登陆的处理"
date: 2022-11-08T22:32:44+08:00
lastmod: 2022-11-08T22:32:44+08:00
draft: false
keywords: [portainer, docker]
description: ""
tags: [docker]
categories: [docker]
author: "徐林"
---

<!--more-->

Portainer是Docker的Web图形化管理工具，提供状态显示面板、应用模板快速部署、容器、镜像、网络、数据卷的基本操作（包括上传下载镜像，创建容器等操作）、事件日志显示、容器控制台操作、Swarm集群和服务等集中管理和操作、登录用户管理和控制等功能。功能十分全面，基本能满足中小型单位对容器管理的全部需求。
### 1、install 安装
就按照官方文档即可，两条命令：<br />`docker volume create portainer_data`
```shell
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```
### 2、安装登陆出错
这里遇到一个问题，我先装了商业版，后改成社区版，发现后台进不去了，loclhost:9443 登陆不了，看了文档，**发现要重新删掉docker volumn 才可以。**
### 3、一句话总结：删掉 docker volumn
