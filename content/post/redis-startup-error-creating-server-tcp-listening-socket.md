---
title: "redis启动出错Creating Server TCP listening socket 127.0.0.1:6379: bind: No error"
date: 2018-02-26T00:00:09+08:00
typora-root-url: ..\..\static
tags: ["redis eror"]
categories: ["redis"]
draft: false
---

## windows下安装redis第一次启动报错：

```base
[16768] 26 Feb 13:54:50.513 # Creating Server TCP listening socket 127.0.0.1:6379: bind: No error
```

## 解决方法：在命令行中运行

```shell
redis-cli.exe

127.0.0.1:6379>shutdown

not connected>exit
```

然后重新运行redis-server.exe redis.windows.conf，启动成功！