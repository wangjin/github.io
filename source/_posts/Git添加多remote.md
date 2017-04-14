---
title: Git添加多remote
date: 2017-04-14 15:06:46
tags:
 - git
 - remote
---

# 添加remote

``` bash
$ git remote add [remote名称] <git_url>
```

# 删除remote

``` bash
$ git remote remove [remote名称] <git_url>
```

# 添加多个同名remote

``` bash
$ git remote set-url --add [remote名称] <git_url>
```

# 删除同名remote

``` bash
$ git remote set-url --delete [remote名称] <git_url>
```

配置保存在<code>.git/config</code>文件中