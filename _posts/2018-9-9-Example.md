---
layout:       post
title:        "Example"
subtitle:     "example thing."
date:         2018-9-9 22:13:20
author:       "garaguru"
header-img:   "img/in-post/post-eleme-example/example.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - Example
    - example
---

<!-- Chinese Version -->
<div class="zh post-container">
    {% capture about_zh %}{% include posts/2018-9-10-example/zh.md %}{% endcapture %}
    {{ about_zh | markdownify }}
</div>

<!-- English Version -->
<div class="en post-container">
    {% capture about_en %}{% include posts/2018-9-10-example/en.md %}{% endcapture %}
    {{ about_en | markdownify }}
</div>