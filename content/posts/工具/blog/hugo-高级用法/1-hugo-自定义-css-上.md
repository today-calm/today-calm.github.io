---
title: "Hugo 自定义 CSS（上）"
date: 2023-07-22T21:04:50+08:00
lastmod: 2023-07-23T14:42:07+08:00
categories: ["工具"]
series: ["Hugo 高级用法"]
series_weight: 1
tags: ["Hugo"]
summary: ""
---

因为要在 [98 五笔易错字](../../../研究/掌握-98-五笔/3-98-五笔易错字.md) 文章中正常展示字根，所以该页面需要单独加载一个字体文件，于是开始了解 Hugo 如何给特定页面添加 CSS

## DoIt 主题为所有页面添加 CSS

在 DoIt 文档中有一段 [自定义样式](https://github.com/HEIGE-PCloud/DoIt/blob/main/exampleSite/content/posts/theme-documentation-basics/index.zh-cn.md#%E8%87%AA%E5%AE%9A%E4%B9%89%E6%A0%B7%E5%BC%8F) 的说明：

> 通过定义自定义 `.scss` 样式文件, **DoIt** 主题支持可配置的样式.
> 
> 包含自定义 `.scss` 样式文件的目录相对于 **你的项目根目录** 的路径为 `assets/css`.
> 
> 在 `assets/css/_override.scss` 中, 你可以覆盖 `themes/DoIt/assets/css/_variables.scss` 中的变量以自定义样式.

确实可以自定义 CSS，但是这是全局生效的效果，我只想改变某个页面的 CSS，那只能再看看 Hugo 有没有这种能力

## Hugo 为特定页面添加 CSS（方法一）

搜到了 2 种方法，本文先介绍第 1 种方法，即在 front matter 中添加变量

### 怎么做

[Custom CSS for each page](https://discourse.gohugo.io/t/solved-custom-css-for-each-page/12476) 这个回答中给出了答案：

1. 在特定 md 文件的 front matter 中添加一个变量，如 `customcss`，值为自定义 CSS 文件的路径（相对于 static 目录）

```yaml
customcss: "/css/custom.css"
```

2. 在 template 中添加以下代码

```html
{{ with .Params.customcss }}
    <link rel="stylesheet" href="{{ . }}" />
{{ end }}
```

看起来步骤很简单，但要理解原理是什么，需要了解 Hugo 的很多知识

### .Params 和 template 的概念

[Page-level params](https://gohugo.io/variables/page/#page-level-params)：front matter 中定义的变量可以通过 `.Params` 访问。也就是说，在 md 文件中定义变量，在 template 中可以拿到这个值

template：即渲染模板，对应 layouts 目录

> [`layouts`](https://gohugo.io/templates/)
> 
> Stores templates in the form of `.html` files that specify how views of your content will be rendered into a static website. Templates include [list pages](https://gohugo.io/templates/lists/), your [homepage](https://gohugo.io/templates/homepage/), [taxonomy templates](https://gohugo.io/templates/taxonomy-templates/), [partials](https://gohugo.io/templates/partials/), [single page templates](https://gohugo.io/templates/single-page-templates/), and more.

默认生效的 template 是 `themes/DoIt/layouts` 内的 html（以 DoIt 主题为例）

如何修改主题的 template：在项目根目录下的 layouts 目录内，新建与主题 template html 相同路径、相同名称的 html，来覆盖主题的 template。通常是将主题的 html 拷贝过去，再加以修改。

```txt
/
├── layouts
│   └── partials
│       └── head
│           └── link.html    
└── themes
    └── DoIt
        └── layouts
            └── partials
                └── head
                    └── link.html
```

### 如何修改模板

搞懂了以上概念，再来看操作步骤。第一步没什么好说的。第二步是在 template 中添加代码，需要考虑修改哪个 html 模板，也就是要找到 html `head` 部分的代码写在了哪个文件里。

在 `themes/DoIt/layouts` 目录下搜索 `head` 关键字，找到了 `themes/DoIt/layouts/_default/baseof.html` 文件，内容大致如下

```html
<head>
    ...

    {{- partial "head/meta.html" . -}}
    {{- partial "head/link.html" . -}}
    {{- partial "head/seo.html" . -}}
</head>
```

可以看到内部通过 `{{- partial "head/link.html" . -}}` 引用了 `themes/DoIt/layouts/partials/head/link.html`，该文件的内容大致如下

```html
{{- /* normalize.css */ -}}
...
{{- partial "plugin/style.html" $style -}}

{{/*  color.min.css  */}}
...
{{- partial "plugin/style.html" $style -}}
```

又通过 `{{- partial "plugin/style.html" $style -}}` 引用了 `themes/DoIt/layouts/partials/plugin/style.html`，再看看这个文件的内容

```html    
    {{- if .Defer -}}
        <link rel="preload" as="style" onload="this.onload=null;this.rel='stylesheet'" href="{{ $href }}" ...>
        <noscript><link rel="stylesheet" href="{{ $href }}" ...></noscript>
    {{- else -}}
        <link rel="stylesheet" href="{{ $href }}" ...>
    {{- end -}}
```

终于看到了 `rel="stylesheet"`，到这里也就把引用关系捋清楚了

回到之前的问题，要修改的 html 就是 `themes/DoIt/layouts/partials/head/link.html`，将文件拷贝到根目录的 layouts 下，在末尾加上以下代码即可

```html
{{- with .Params.customcss -}}
    <link rel="stylesheet" href="{{ . }}" />
{{- end -}}
```

注：在 `layouts/partials/plugin/style.html` 中无法取到 `.Params.customcss` 的值，可能和 partial 的使用有关，没详细了解

## Hugo 为所有页面添加 CSS

参考 [Add Custom CSS Or Javascript To Your Hugo Site](https://www.banjocode.com/post/hugo/custom-css)

原理和上一小节一样，只是把变量写到 config.toml 中，模板中通过 `.Site.Params` 取值

## 参考

[Hugo添加自定义css和javascript](https://finisky.github.io/hugoaddcssjs/#hugo%E6%B7%BB%E5%8A%A0%E8%87%AA%E5%AE%9A%E4%B9%89cssjs%E7%9A%84%E6%AD%A3%E7%A1%AE%E6%96%B9%E5%BC%8F)
