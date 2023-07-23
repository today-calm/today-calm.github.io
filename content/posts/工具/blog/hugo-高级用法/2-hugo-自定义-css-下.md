---
title: "Hugo 自定义 CSS（下）"
date: 2023-07-23T10:26:13+08:00
lastmod: 2023-07-23T14:09:40+08:00
categories: ["工具"]
series: ["Hugo 高级用法"]
series_weight: 2
tags: ["Hugo"]
summary: ""
---

## Hugo 为特定页面添加 CSS（方法二）

[前文](1-hugo-自定义-css-上.md) 说到 Hugo 为特定页面添加 CSS 有 2 种方法，本文介绍第 2 种方法，即利用 page bundles

### 怎么做

[Hugo: can I apply a custom css style to a specific post?](https://stackoverflow.com/questions/71259337/hugo-can-i-apply-a-custom-css-style-to-a-specific-post)

```html
{{ with .Resources.GetMatch "*.css" }}
  <style>{{ .Content | safeCSS }}</style>
{{ end }}
```

[Custom css for certain page using Page Resources](https://discourse.gohugo.io/t/custom-css-for-certain-page-using-page-resources/39526)

### page bundles、page resource 是什么

这里需要理解 [page bundles](https://gohugo.io/content-management/page-bundles/) 和 [page resource](https://gohugo.io/content-management/page-resources/) 两个概念

page bundles 有 leaf bundle、branch bundle 两种类型，这里只看 leaf bundle

leaf bundle 就是 `index.md` 文件和其他类型资源共同组成的目录，例如下面的 `about`、`my-post`、`my-other-post`、`another-leaf-bundle` 都是 leaf bundle

```txt
content/
├── about
│   ├── index.md
├── posts
│   ├── my-post
│   │   ├── content1.md
│   │   ├── content2.md
│   │   ├── image1.jpg
│   │   ├── image2.png
│   │   └── index.md
│   └── my-other-post
│       └── index.md
│
└── another-section
    ├── ..
    └── not-a-leaf-bundle
        ├── ..
        └── another-leaf-bundle
            └── index.md
```

page resource 就是上面说的其他类型资源（jpg/png...文件），不同 page bundle 的 page resource 是互相隔离的

这么讲还是不知道 page bundles 有什么用，[HUGO PAGE BUNDLES: THE BASICS](https://www.hackification.io/software-development/hugo/page-bundles/the-basics/) 这篇文章讲的比较清楚

page bundles 的作用：
1. 在 Hugo 中，MarkDown 引用的资源通常放在 static 目录下，但这会比较混乱。使用 page bundles 的结构能让 MarkDown 和其引用的资源放在一起，结构更清晰
2. 在模板中可以通过  `.Resources.GetMatch` 访问资源文件的内容，从而拼装到模板中，下方代码是个例子
```html
{{ with .Resources.GetMatch "script.js" }}
  <script>{{ .Content | safeJS }}</script>
{{ end }}

{{ with .Resources.GetMatch "style.css" }}
  <style>{{ .Content | safeCSS }}</style>
{{ end }}

{{ with .Resources.GetMatch "img.png" }}
  <img src="data:{{ .MediaType.Type }};base64,{{ .Content | base64Encode }}">
{{ end }}
```

现在就能理解开头那段代码了，其实就是把目录结构组织成 page bundle 的形式，把 CSS 文件放到 bundle 目录下，作为 page resource。再到模板中通过 `.Resources.GetMatch "*.css"` 获取到该文章对应的 CSS 文件，这样就可以一篇文章对应一个 CSS 了。

## 对比下两种方法

将 [前文](1-hugo-自定义-css-上.md) front matter 添加变量的方法与本文 page bundle 的方法对比一下

front matter 添加变量的优点：
- 通过 front matter 中的变量是否存在，来控制是否加载 CSS，比较优雅
- 相比 page bundle，不用改变 content 目录结构，不需要改变 Obsidian 的使用习惯
- CSS 文件放到 static 目录下，可被多篇文章复用

page bundle的优点：
- 比较直观，page resource 就是用来放到模板里的，通过 CSS 文件是否存在，来控制是否加载 CSS

两种方法的共同点：
- CSS 文件通过某种方式与特定 MarkDown 建立关联
- 在模板中写入条件渲染样式的代码，使用 Hugo 模板 API 获取与文章关联的 CSS 文件

由于我需要配合 Obsidian 使用，所以选择了 front matter 添加变量的方法
