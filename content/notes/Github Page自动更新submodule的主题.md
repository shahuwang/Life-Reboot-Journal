---
date: 2025-10-10
draft: false
tags:
- GithubPage
- Hugo
title: Github Page自动更新submodule的主题
---

搞好了Github page的项目之后，回家下载了项目，要跑起来的时候，总是提示如下内容：

    WARN  found no layout file for "html" for kind "home": You should create a template file which matches Hugo Layouts Lookup Rules for this combination.
    WARN  found no layout file for "html" for kind "section": You should create a template file which matches Hugo Layouts Lookup Rules for this combination.
    WARN  found no layout file for "html" for kind "taxonomy": You should create a template file which matches Hugo Layouts Lookup Rules for this combination.
    WARN  found no layout file for "html" for kind "term": You should create a template file which matches Hugo Layouts Lookup Rules for this combination.

而且页面实际上没有渲染成功，一直都是老页面，又找不出哪里出错了。
最终发现主题因为是用submodule安装的，在新电脑上不会自动下载，需要执行下面的命令：

``` bash
git submodule init
git submodule update
```

执行后就会自动下载主题了
