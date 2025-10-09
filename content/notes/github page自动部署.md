---
title: "github page自动部署"
date: 2025-10-09
draft: false
tags: ["hugo"]
---

在项目的settings页面，找到Pages，把 Build and deployment改成 Github Actions.

然后在 Use a suggested workflow, browse all workflows，找到hugo，点击生成默认的 workflows，然后把这个workflows的hugo.yaml做如下修改：

1、HUGO_VERSION 改成与自己的版本一致

2、baseUL改成：`--baseURL "https://${{ github.repository_owner }}.github.io/${{ github.event.repository.name }}/"`，原来的方式是主page级别，我要的是项目级的page.

后续只要git push就会自动创建部署了。在这个页面可以看到部署进度 https://github.com/shahuwang/Life-Reboot-Journal/deployments/github-pages。

这样子后续在没有hugo的电脑上，只要编辑文件的markdown就可以了，github Action会自动生成html。