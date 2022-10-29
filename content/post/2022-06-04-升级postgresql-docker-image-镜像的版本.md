---
title: 升级 postgresql docker image 镜像的版本
slug: 升级 postgresql-docker-image-镜像的版本
description: null
author: fatcat
date: '2022-06-04T13:05:10.331Z'
lastmod: 2019-08-22T15:20:28.000Z
tags: [docker, postgresql, rails]
categories: [docker, rails, postgresql]
---

### 问题
  直接在 `docker-compose.yml` 中修改版本后，`docker-compose up` 运行不起来，会报错。

> 	FATAL: database files are incompatible with server
   	DETAIL: The data directory was initialized by PostgreSQL version 13, which is not compatible with this version 14.3.

### 解决方法
  - 删掉 volume，重新构建。
	- 删除 volume 要先删除 container 容器，否则会报错：该 volume in use.
	- 运行命令：
    ```yaml   
        docker container stop image_name
        docker container rm image_name
        docker volume rm image-vol
    ```
- 然后重新运行 `docker-compose up；rails db:migrate db:seed`