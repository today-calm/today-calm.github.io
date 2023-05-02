---
title: "用 Hugo Render Hooks 简化 Markdown 中链接、图片的引用"
date: 2023-05-02T14:20:34+08:00
lastmod: 2023-05-02T23:55:07+08:00
categories: ["工具"]
tags: ["Hugo", "Obsidian"]
summary: "Render Hooks 可以允许 Markdown 中链接、图片使用相对路径引用，保证资源在 Obsidian、GitHub Pages、GitHub 仓库均正常展示"
---

## 背景

在 Obsidian 中链接自己的其他文章，Obsidian 能够正常跳转，但在 Hugo 中无法正确跳转，因为 Hugo 构造的链接路径有问题。之前在 [Hugo Obsidian 结合实践](hugo_obsidian_结合实践.md#使用图片资源) 文章中有处理过图片引用的问题，但没有解决文章引用的问题。

搜索了下，[这个帖子](https://discourse.gohugo.io/t/how-to-render-markdown-url-with-md-to-correct-link/26372)提供了解决方案，即利用 [Markdown Render Hooks](https://gohugo.io/templates/render-hooks/) 修正 Hugo 生成的链接格式，同时也提供了一个[允许相对路径引用的例子](https://github.com/bep/portable-hugo-links/tree/master/layouts/_default/_markup)。

## 开始之前

之前我在 Obsidian 中配置了链接使用绝对路径的格式，但在了解到可以通过 Render Hooks 来用相对路径引用资源后，我打算把 Obsidian 再改成使用相对路径格式。

原因主要是绝对路径有弊端：在 GitHub 仓库中或 VSCode 中查看文章，均无法展示图片，文章跳转也不正确。因为 Obsidian 的根目录只是 Hugo 项目的一个子目录，而非 Hugo 项目的根目录，绝对路径并不“绝对”。

那么把一些配置恢复原状即可：
1. 在 Obsidian 中找到 `New link format`，修改为 `Relative path to file`
2. 将 config.toml 中 mount static/assets 的代码删除
3. Obsidian Linter 插件不再配置 Custom Regex Replacement 来给链接开头加上 `/`

接下来进入正题

## Render Hooks 介绍

Render Hooks 这个概念并不复杂，其实就是让用户覆盖默认的渲染行为，自定义 markdown 渲染函数  
Render Hooks 支持 image、link、heading、codeblock 这 4 种 markdown 元素类型  
创建 Render Hooks 只需要在 `layouts/_default/_markup` 目录下创建名称为 `render-{kind}` 的 html 文件即可

```txt
layouts/
└── _default/
    └── _markup/
        ├── render-codeblock.html
        ├── render-heading.html
        ├── render-image.html
        └── render-link.html
```

接着看一下 Render Hooks 的内容，我们这里只关注 render-image.html 和 render-link.html，下面是 [官网的两个例子](https://gohugo.io/templates/render-hooks/#link-with-title-markdown-example)

layouts/_default/_markup/render-link.html
```html
<a href="{{ .Destination | safeURL }}"{{ with .Title }} title="{{ . }}"{{ end }}{{ if strings.HasPrefix .Destination "http" }} target="_blank" rel="noopener"{{ end }}>
  {{ .Text | safeHTML }}
</a>
```

layouts/_default/_markup/render-image.html
```html
<p class="md__image">
  <img src="{{ .Destination | safeURL }}" alt="{{ .Text }}" {{ with .Title }} title="{{ . }}"{{ end }} />
</p>
```

注：
- 看懂以上代码需要先了解 [Go Templates](https://gohugo.io/templates/introduction/) 的语法（[Hugo Parameters](https://gohugo.io/templates/introduction/#hugo-parameters) 前面的小节都是重点）
- 直接看 Go Templates 其实也会比较懵逼的，因为其中有用到一些 Go 语言的语法（比如 `:=`），文档里并没有讲，所以最好先了解一下 Go 的语法（推荐网站 [Learn Go in Y Minutes](https://learnxinyminutes.com/docs/zh-cn/go-cn/)）

上面的代码用到了 variable、function、logic、context、pipe 的语法，把这些语法搞清楚，再看代码就很清晰了，这里就不解释了

## 实践：使用相对路径引用链接、图片

[背景](#背景) 部分已经给出了[例子](https://github.com/bep/portable-hugo-links/tree/master/layouts/_default/_markup)，但那个例子的代码比较难看懂，原因是：
- 没有缩进，看不清楚逻辑关系
- 有图片尺寸处理的代码，需要前置知识才能看懂，包括 [Image Processing](https://gohugo.io/content-management/image-processing/)、[Hugo Pipes Introduction](https://gohugo.io/hugo-pipes/introduction/)
- 不熟悉一些 Hugo functions 的功能

那么直接看我改造后的版本吧

render-link.html
```html
{{ $link := .Destination }}
{{ $isRemote := strings.HasPrefix $link "http" }}
{{- if not $isRemote -}}
  {{ $url := urls.Parse .Destination }}
  {{- if $url.Path -}}
    {{ $fragment := "" }}
    {{- with $url.Fragment }}{{ $fragment = printf "#%s" . }}{{ end -}}
    {{- with .Page.GetPage $url.Path }}{{ $link = printf "%s%s" .RelPermalink $fragment }}{{ end -}}
  {{- end -}}
{{- end -}}
<a href="{{ $link | safeURL }}"{{ with .Title}} title="{{ . }}"{{ end }}{{ if $isRemote }} target="_blank"{{ end }}>{{ .Text | safeHTML }}</a>
```

注：
- 区分 http 链接和本地链接 2 种情况
- `$fragment` 表示锚点
- 通过 `.Page.GetPage` 和 `.RelPermalink` 拿到了最终的链接

render-image.html
```html
{{ $path := .Destination }}
{{ $isRemote := strings.HasPrefix $path "http" }}
{{- if not $isRemote -}}
  {{ $path = path.Join .Page.File.Dir .Destination }}
  {{ $path = printf "/%s" $path }}
{{- end -}}
<figure>
  <img src="{{ $path }}" alt="{{ $.Text }}" />
  {{ with $.Title | safeHTML }}
    <figcaption class="image-caption">{{- . -}}</figcaption>
  {{ end }}
</figure>
```

注：
- 区分 http 图片和本地图片 2 种情况
- `$path` 开头添加的 `/` 很重要，表示绝对路径。如果不加的话，会视为相对路径，渲染成 html 后会在前面自动加上当前文章的路径，导致无法访问到
- DoIt 主题也有自定义 Render Hooks，我们在根目录下定义的 Render Hooks 会覆盖主题的，因此可能会丢失一些主题的功能。主题的 Render Hooks 代码有点复杂，没太看懂。  
  测试发现功能没影响，但图片居中的样式丢了。看了下主题的 CSS 选择器，在 img 标签外面套上 figure 就好了。
