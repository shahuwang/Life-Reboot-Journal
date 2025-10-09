---
title: "添加icon"
date: 2025-10-09
draft: false
tags: ["hugo"]
---

生成一个icon图片，命名为 favicon.ico，放到 static/images/favicon.ico。

然后在 hugo.yaml 配置里加上：

```
params:
  favicon: "images/favicon.ico"
```

这样子就有了icon了