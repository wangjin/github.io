---
title: Docker容器网络
date: 2018-05-08 13:46:17
tags:
 - Docker
 - 容器
---

# 默认网络
Docker服务安装时，会自动创建3个网络。可以使用`docker network ls`查看。
```bash
docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
1b2f89560748        bridge              bridge              local
07b04b912109        host                host                local
87756913dcc7        none                null                local
```

这3个网络