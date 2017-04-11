---
title: Mac上通过nvm安装Node.js
date: 2017-04-11 17:22:12
tags:
- Mac
- OSX
- Node
- Node.js
- nvm
---

# 安装nvm（0.33.1版本）
``` bash
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash
```
具体版本查看[GitHub](https://github.com/creationix/nvm)

# 查看可安装版本
``` bash
$ nvm ls-remote
```

# 安装Node.js（7.8.0版本）
``` bash
$ nvm install v7.8.0
```

# 查看本地已安装的版本
``` bash
$ nvm ls
```


# 切换默认Node.js版本
``` bash
$ nvm alias default v7.8.0
```

# 查看Node.js版本
``` bash
$ node -v
$ npm -v
```
