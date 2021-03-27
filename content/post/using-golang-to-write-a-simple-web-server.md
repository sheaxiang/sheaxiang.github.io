---
title: "使用golang写简单的web服务器"
date: 2019-01-25T00:50:55+08:00
typora-root-url: ..\..\static
tags: ["golang", "web服务器"]
categories: ["Golang"]
draft: false
---

golang是一门用了就不想停下来的语言,简单实用易部署,我也开始了学习golang的路程,因为我主php,学习一门新语言难免会涉及到web.话不多说,直接上代码

```go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func say(w http.ResponseWriter, r *http.Request) {
	r.ParseForm()
	fmt.Println("path", r.URL.Path)

	fmt.Fprintf(w, "<b>"+r.URL.Path+"</b>") //这个写入到w的是输出到客户端的
}

func main() {
	http.HandleFunc("/", say)
	err := http.ListenAndServe(":9999", nil)
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
```

是不是觉得很简单,`golang`有很多好用的官方内置包,帮助我们实现各种功能,上面的代码实现了一个简单的web服务器,并监听路由进行输出,只需要使用命令`go run`就可运行了,在浏览器输入[http://127.0.0.1:9999/](http://127.0.0.1:9999/)即可访问