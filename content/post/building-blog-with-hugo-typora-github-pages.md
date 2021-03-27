---
title: "使用Hugo, Typora, GitHub Pages搭建博客"
date: 2021-03-27T18:29:08+08:00
tags: ["hugo", "typora", "github"]
categories: ["教程"]
draft: false
typora-root-url: ..\..\static
---

## 简述

在没有服务器和域名的情况下,想自己写博客,需要怎么做呢? 答案是使用`Hugo`, `Typora`, `GitHub`来搭建属于自己的博客,我们先来看下各自简介

[Hugo](https://gohugo.io/): 是一个使用`golang`语言编写的静态站点生成器,它可以把`markdown`文件转换成`html`文件,再通过上传到`GitHub`上来达到生成博客的目的

[Typora](https://www.typora.io/): 是一个功能强大的 `MarkDown` 文本编辑器,可在`mac OS X`, `WIndows`, `Linux`平台下使用,可以使用此软件对`Hugo`生成的.md文件进行编辑

[GitHub Pages](https://pages.github.com/): 是由GitHub提供的用于托管个人网站的静态页面,只需要在GitHub上创建仓库,push,即可更新网站内容,需要注意的是:`此网站可能会间接性不能访问`



## 流程

安装过程不再赘述,这里给出下载地址:

Hugo: [https://github.com/gohugoio/hugo/releases](https://github.com/gohugoio/hugo/releases)

Typora Windows: [https://www.typora.io/#windows](https://www.typora.io/#windows) 

Typora Linux: [https://www.typora.io/#linux](https://www.typora.io/#linux)

### Hugo 快速开始

#### 检验hugo是否已安装,如果没有,请安装

```shell
hugo version

# Hugo Static Site Generator v0.68.3/extended linux/amd64 BuildDate: 2020-03-25T06:15:45Z
```

#### 创建新站点

```shell
hugo new site shea

# Congratulations! Your new Hugo site is created in /shea.

# Just a few more steps and you're ready to go:

# 1. Download a theme into the same-named folder.
#   Choose a theme from https://themes.gohugo.io/ or
#   create your own with the "hugo new theme <THEMENAME>" command.
# 2. Perhaps you want to add some content. You can add single files
#   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
# 3. Start the built-in live server via "hugo server".

# Visit https://gohugo.io/ for quickstart guide and full documentation.
```

#### 添加主题

我现在使用的主题是 [https://themes.gohugo.io/hugo-theme-even/](https://themes.gohugo.io/hugo-theme-even/),安装主题很简单,只需要把主题下载下来,然后放到`theme`文件夹里,然后配置下就可以了

```shell
# 在站点根目录执行
git clone https://github.com/olOwOlo/hugo-theme-even themes/even

# 更新主题
cd ./themes/even/
git pull
```

配置: 

- 在主题的 `exampleSite` 目录下有个`config.toml`文件,需将这个文件的内容复制到站点根目录下`(根目录下也有一个同名文件)`,然后根据需求更改,具体怎么配置的话,可以直接看文件内容,都有注释的,这里就不再赘述
- 对于这个主题，你应该使用 post 而不是 posts，即 `hugo new post/some-content.md`。

#### 创建第一篇文章

```shell
# 需要注意的是,这里我把 posts 换成了 post,因为主题需要
hugo new post/my-first-post.md
```

执行该命令会生成包含如下文件内容:

```
---
title: "My First Post"
date: 2019-03-26T08:47:11+01:00
draft: true
---

这里我们更改 draft:false
```

#### 启动 Hugo 服务

```shell
hugo serve

# Building sites … 

#                   | EN
#-------------------+-----
#  Pages            |  3
#  Paginator pages  |  0
#  Non-page files   |  0
#  Static files     |  0
#  Processed images |  0
#  Aliases          |  0
#  Sitemaps         |  1
#  Cleaned          |  0

# Built in 13 ms
# Watching for changes in /mnt/c/Users/Administrator/Desktop/sheaxiang/{archetypes,content,data,layouts,static}
# Watching for config changes in /mnt/c/Users/Administrator/Desktop/sheaxiang/config.toml
# Environment: "development"
# Serving pages from memory
# Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
# Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
# Press Ctrl+C to stop
```

这里我们访问[http://localhost:1313/](http://localhost:1313/),就可以看到网站了

#### 构建静态文件, 在 `public`目录下

```shell
hugo

# Building sites … 

#                    | EN
# -------------------+-----
#   Pages            |  3
#   Paginator pages  |  0
#   Non-page files   |  0
#   Static files     |  0
#   Processed images |  0
#   Aliases          |  0
#   Sitemaps         |  1
#   Cleaned          |  0

# Total in 24 ms
```



### Typora快速开始

简单的使用就自己摸索,这里主要是介绍`插入本地图片`如何与`Hugo`结合

#### 插入本地图片

首先,我们需要知道的是,Hugo的`静态文件`存放目录是在`static`, 现在我们打开 `Typora`软件,在左上角点击 文件 -> 偏好设置 -> 图像,按照如下图设置:

![Typora图像设置](/images/building-blog-with-hugo-typora-github-pages.assets/rOcUdn4rh9HnrYKl.png)

点击`文件夹图标`选择站点下的static文件夹, 这里的`${filename}`是md文件的文件名,可根据自己的需求配置

然后再点击 `格式->图像->设置图片根路径`, 选择你站点下的`static`文件夹,就可以正常插入图片并正常显示了

还有一点`需要注意`的是,使用Typora插入本地图片时,请注意插入`图片的文件名`,如果像是qq截图这种生成的文件名(带[] 这种特殊符号),可能会导致插入失败

### GitHub Pages快速开始

在[github](https://github.com/)上注册账号这些就不说了,下面我们来说创建GitHub Pages

在仓库中新建仓库名为: `github用户名.github.io`

然后进入该仓库,点击 `Settings`,往下拉显示:

![github pages settings](/images/building-blog-with-hugo-typora-github-pages.assets/@QNHO`}[1UH0W1MJ9XTGU_N.png)

这时候访问站点,会提示`404`,因为仓库中没有`index.html`文件

我们把仓库克隆下来,然后把刚才站点的文件全部复制进去,然后提交,这时候访问还是会404,你仔细观察会发现,Hugo构建的静态文件在`public`文件夹,在一个`二级目录`,而GitHub Pages没有选项可以选择二级目录(gitee可以,但是提交代码更新了,需要手动去刷新重新部署),但是会发现,有选择分支的选项,这我们可以利用起来,在git中有个`subtree`,具体使用可以翻阅文档,我们先来实现

```shell
# 站点根目录执行
git subtree push --prefix public origin public
```

第一个public 是当前项目的文件夹,第二个是远程分支的名称

这时我们再在仓库的github pages设置里选择分支为public,点击save,稍等片刻,就可以正常访问了



### 附录

#### 配置默认生成模板

在 `archetypes/default.md`文件里,我们可以配置新创建的文件的初始内容

```
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
typora-root-url: ..\..\static
tags: []
categories: []
draft: true
---
```

这里新增了 tags (`标签`), categories(`分类`), typora-root-url (`这是我们刚才配置的插入本地文件配置的根路径`)
