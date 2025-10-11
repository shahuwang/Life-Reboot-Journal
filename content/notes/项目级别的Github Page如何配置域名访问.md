---
date: "2025-10-11T20:50:00+08:00"
draft: false
title: 项目级别的Github Page如何配置域名访问
---

因为打算建多个网站，所以不可能使用用户级别的Github Page项目，只能使用项目级别的。我在cloudflare上购买了域名（确实便宜划算，功能支持多），想把域名绑定到这个项目级别的网站上，折腾了一番，终于搞定了。下面是操作过程。
<!--more-->
首先，在Github项目上打开 Settings-\>Pages, 点击 [Learn more about configuring custom domains](https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site). 查看帮助文档，主要在这篇 [Managing a custom domain for your GitHub Pages site - GitHub Docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site) 上找到要配置的A记录和AAAA记录的IP即可。

然后到cloudflare上，对域名做如下的配置：

| 类型  | 名称 | 值                  | 代理状态 |
|-------|------|---------------------|----------|
| A     | @    | 185.199.108.153     | ☁️ 灰色  |
| A     | @    | 185.199.109.153     | ☁️ 灰色  |
| A     | @    | 185.199.110.153     | ☁️ 灰色  |
| A     | @    | 185.199.111.153     | ☁️ 灰色  |
| AAAA  | @    | 2606:50c0:8000::153 | ☁️ 灰色  |
| AAAA  | @    | 2606:50c0:8001::153 | ☁️ 灰色  |
| AAAA  | @    | 2606:50c0:8002::153 | ☁️ 灰色  |
| AAAA  | @    | 2606:50c0:8003::153 | ☁️ 灰色  |
| CNAME | www  | fortisor.com        | ☁️ 灰色  |

要记得把代理状态给关闭掉，这个是cloudflare提供的高级功能，但是Github Page不支持这个功能。

域名配置好之后，等给5分钟左右，再到项目的 Settings------》Pages，在Custom domain处输入你的域名，譬如我的是 fortisor.com, 等待github校验域名成功后，再勾选上 Enforce HTTPS，这样子域名就算配置完成了，现在解决项目级的资源访问配置问题。

首先在 hugo.yaml 的配置里改成如下配置：

    baseURL: https://fortisor.com/
    timeZone: "Asia/Shanghai"
    disablePathToLower: true

因为我使用了Github Action自动部署，需要修改 .github/workflows/hugo.yaml
主要改下run命令里的baseURL：

    --baseURL "https://fortisor.com/"

这样子自动构建的时候，就会重新生成基于主域名的资源应用路径了。

提交代码，很快就可以使用了。
