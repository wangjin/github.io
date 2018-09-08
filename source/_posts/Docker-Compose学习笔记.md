---
title: Docker Compose学习笔记
date: 2018-09-08 14:16:36
tags:
 - Docker
 - Docker Compose
---

# 总览
`Docker Compose`是一个用于定义和运行多容器Docker应用程序的工具。通过使用YAML文件来配置应用程序的服务，并使用`docker-compose`相关命令从配置中创建并启动所有服务。

使用Compose通常分为以下三部：
 1. 使用`Dockerfile`定义应用
 2. 使用`docker-compose.yml`定义服务
 3. 使用`docker-compose up`构建并启动应用

`docker-compose.yml`配置文件一般长这样：
```xml
version: '3'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```


