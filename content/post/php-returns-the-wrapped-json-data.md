---
title: "php返回换行的json数据"
date: 2019-02-21T00:55:10+08:00
typora-root-url: ..\..\static
tags: ["php", "json", "php基础"]
categories: ["PHP"]
draft: false
---

今天刚好需要返回拼接数据,展示一大堆数据,需要用到换行,原来想着使用html标签:`<br/>`,不行,也用过\r,也不行,后面想到了这样:

```
\r\n
例子:"aaa\r\nbbbb"
```

