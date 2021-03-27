---
title: "在Windows下设置Redis服务"
date: 2018-02-26T00:05:07+08:00
typora-root-url: ..\..\static
tags: ["redis服务", "windows"]
categories: ["Redis"]
draft: false
---

Redis常规启动为运行命令：

```shell
redis-server redis.windows.conf
```

但这样如果cmd窗口关闭，Redis会消失，所以要将Redis放入Windows服务，需运行命令：

```shell
redis-server --service-install redis.windows-service.conf --loglevel verbose
```

输入命令之后没有报错，表示成功了，刷新服务，会看到多了一个redis服务。