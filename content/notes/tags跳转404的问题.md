---
title: "使用Diary主题解决tag页面跳转404的问题"
date: 2025-10-09
draft: false
tags: ["hugo"]
---

Diary主题默认所有项目都是主github page，但我的是项目级别的github page，页面的tags标签自动跳转时不带项目名称，导致跳转url为404页面。

只需要在 theme/hugo-theme-diary/layouts/_default/single.html里，把如下这一行：

` <a href="{{ "/tags/" | relLangURL }}{{ . | urlize }}">{{ . }}</a>`

替换成：

` <a href="{{ "/Life-Reboot-Journal/tags/" | relURL }}{{ . | urlize }}">{{ . }}</a>`

即可解决tags跳转失败的问题。