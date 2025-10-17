---
date: "2025-10-17T10:09:52+08:00"
draft: false
title: hugo实现图片的轮播效果
---

搞了很久，因为会和主题有冲突等情况，导致chatGPT和Gemini实现的方案都不可用，最终再多番尝试后终于实现了，使用方式如下：

##### 一、新增layouts目录

新增layouts一级目录，然后再建一个子目录 layouts/shortcodes
\##### 二、新增swiper.html
新增 layouts/shortcodes/swiper.html，代码如下：

``` html
<div class="swiper mySwiper">
  <div class="swiper-wrapper">
    {{- range .Page.Params.swiper_images }}
    <div class="swiper-slide">
      <img src="{{ . }}" alt="">
    </div>
    {{- end }}
  </div>
  <div class="swiper-pagination"></div>
  <div class="swiper-button-prev"></div>
  <div class="swiper-button-next"></div>
</div>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/swiper@10/swiper-bundle.min.css" />
<script src="https://cdn.jsdelivr.net/npm/swiper@10/swiper-bundle.min.js"></script>
<script>
document.addEventListener('DOMContentLoaded', function () {
  var swiper = new Swiper(".mySwiper", {
    loop: true,
    pagination: { el: ".swiper-pagination", clickable: true },
    navigation: { nextEl: ".swiper-button-next", prevEl: ".swiper-button-prev" },
  });
});
</script>
```

这里的关键点，就是引入 swiper的css和js，要直接放到swiper.html里，如何放到 layouts/partial/head.html里的话，会和我的主题冲突，造成setTheme函数失败。
\#### 三、使用方法

在markdown文件里，增加如下头部：

``` markdownn
---
date: "2025-10-15T19:42:08+08:00"
draft: false
title: test
swiper_images:
  - "0.png"
  - "1.png"
  - "2.png"
---
{{< swiper> }}
```
