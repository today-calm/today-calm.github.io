---
title: "DoIt 主题添加系列（series）"
date: 2023-07-23T14:24:39+08:00
lastmod: 2023-07-23T14:31:31+08:00
categories: ["工具"]
series: ["使用 Hugo 与 Obsidian 搭建博客"]
series_weight: 5
tags: ["Hugo"]
summary: ""
---

修改 config.toml

```toml
  [params.page]
    # 是否在文章开头显示系列导航
    seriesNavigation = true

...

  [[menu.main]]
    identifier = "series"
    name = "系列"
    url = "/series/"
    pre = "<i class='fas fa-book fa-sm'></i>"
    weight = 3

...

[taxonomies]
  series = "series"
```

在需要加入一个系列的文章的 front matter 中，添加如下变量

```yaml
series: ["系列名称"]
series_weight: 1
```

series_weight 表示该文章在系列中的权重，权重越大，越靠后展示
