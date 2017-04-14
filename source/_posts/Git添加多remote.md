---
title: Git添加多remote
date: 2017-04-14 15:06:46
tags:
 - git
 - remote
---

# Git添加remote

``` bash
$ git remote add [remote名称] <git_url>
```

# Git删除remote

``` bash
$ git remote remove [remote名称] <git_url>
```

# Git添加多个同名remote

``` bash
$ git remote set-url --add [remote名称] <git_url>
```

# Git删除同名remote

``` bash
$ git remote set-url --delete [remote名称] <git_url>
```