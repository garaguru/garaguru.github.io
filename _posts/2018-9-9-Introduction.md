---
layout:       post
title:        "博客简介/Introduction"
subtitle:     "An Introduction of This Blog"
date:         2018-9-9 22:13:20
author:       "garaguru"
header-img:   "img/in-post/post-eleme-introduction/default.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - Introduction
---

<!-- Chinese Version -->
<div class="zh post-container">
    {% capture about_zh %}{% include posts/2018-9-9-introduction/zh.md %}{% endcapture %}
    {{ about_zh | markdownify }}
</div>

<!-- English Version -->
<div class="en post-container">
    {% capture about_en %}{% include posts/2018-9-9-introduction/en.md %}{% endcapture %}
    {{ about_en | markdownify }}
</div>
