---
title: "Hugo 基本使用"
date: 2023-04-01T15:50:55+08:00
lastmod: 2023-07-23T13:11:22+08:00
categories: ["工具"]
series: ["使用 Hugo 与 Obsidian 搭建博客"]
series_weight: 1
tags: ["Hugo"]
summary: "世界上最快的 SSG 框架 Hugo 的使用方法"
---

官方文档：[https://gohugo.io/documentation/](https://gohugo.io/documentation/)

## 特点

优点：
- 为静态页面构建而生
- 速度极快：使用 go 语言编写，当博客数量很多时，比 hexo 构建速度快很多
- 安装简单：无额外依赖，不需要像 hexo 一样安装 node_modules
- 提供了众多部署方案
- 社区庞大

缺点：
- 优质主题太少，这点不如 hexo
- 文档晦涩：在介绍一个概念时引入了好几个你没见过的概念，文档也不够详细。甚至有人专门写文章吐槽 [Why Hugo's Documentation Sucks](https://sagar.se/blog/hugo-documentation/)

## 核心概念

基础：
- site：使用 `hugo new site` 创建的项目  
- post/page：一篇文章  
- publish：构建资源到 public 目录下  
- deploy：部署资源到服务器上  
- 配置文件：支持 toml、yaml、json 格式，不同主题可能会推荐用不同的格式

目录结构：
- archetypes：`hugo new posts/xxx` 时使用的模板
- assets：存放待处理的资源，这些文件会经过 hugo pipes 处理（没具体了解）
- layouts：渲染的 html 骨架，一般由主题提供
- static：存放静态资源，markdown 中引用的图片可以放在这

[Content Management](https://gohugo.io/content-management/)：
- page bundles：一种目录的组织结构，分为 leaf bundle 和 branch bundle
- leaf bundle：有 index.md 的目录，即普通文章
- branch bundle：有 `_index.md` 的目录，一般是 home page/section/taxonomy terms/taxonomy list
- content types：不同 type 有不同的渲染方式（layout），默认使用 content 目录下的子目录名作为 type
- content sections：content 目录下的非叶子节点目录
- taxonomies：分类，默认支持 tags、categories，给某篇文章指定 tags/categories 需要在 front matter 中声明

[Hugo Modules](https://gohugo.io/hugo-modules/)：Hugo 模块，由 static、content、layouts、data、assets、i18n、archetypes 这 7 种类型的 component 组成。整个项目也算是 Hugo Module。  
[Module mounts](https://gohugo.io/hugo-modules/configuration/#module-config-mounts)：Hugo 可以将 module 中的目录重新映射到一个虚拟文件系统，这在配合 Obsidian 时很有用。Obsidian 的目录结构和 Hugo 是不一致的，但通过 mount 就可以解决这个问题。

## 安装

macOS 安装方法：
1. 前置依赖：git、go  
2. 官方提供了多种安装方式，这里选择下载 Prebuilt binaries 的方式。从 github release 下载 extended 版本（官方推荐，某些主题也需要），将二进制文件放到 `/usr/local/bin` 下，该目录已在 PATH 环境变量中  
3. 使用 `hugo version` 验证是否安装成功

## 基本使用

创建项目：
```bash
hugo new site xxx
cd xxx
git init
```

创建文章：
```bash
hugo new posts/first-post.md
```
注：创建文章时会使用 archetypes 中的模板

一篇文章的结构：
```yaml
---
title: "My First Post"
date: 2022-11-20T09:03:20-08:00
draft: true
---

正文...
```

本地启动服务器：
```bash
hugo server
```
注：配置 `draft: true` 的文章默认无法看到，需要在命令后加上 `--buildDrafts` 才能看到。为了方便，可以在模板中去掉该字段。

publish：  
```bash
hugo
```
注：我选择部署到 GitHub Pages，不需要执行该命令，直接 deploy 即可。

deploy：  
配置好 GitHub Actions 后，push 即可，参考 [Host on GitHub](https://gohugo.io/hosting-and-deployment/hosting-on-github/) 

## 主题

hugo 的优质主题较少，且很多主题不再维护，找到一个合适的主题还挺困难的。  
一开始使用了 star 最多的 PaperMod 主题，样式有点太单调了，文档也太简单。  
之后使用了 DoIt 主题，样式更丰富，文档更清晰，目前仍在维护。

仓库：[https://github.com/HEIGE-PCloud/DoIt](https://github.com/HEIGE-PCloud/DoIt)  
文档：[https://hugodoit.pages.dev/zh-cn/](https://hugodoit.pages.dev/zh-cn/)

安装：
```bash
git submodule add https://github.com/HEIGE-PCloud/DoIt.git themes/DoIt
```
注：[防止 VSCode 总显示 submodule 仓库](https://github.com/microsoft/vscode/issues/107398#issuecomment-1349631335)  

参考官方文档中的完整配置，根据自己的需要修改配置即可，仓库中的 exampleSite 目录是一个很好的模仿对象

下面记录几个遇到的坑：
1. 必须显示指定 `keepStatic = false`，否则无法显示侧边目录，这点文档没有说明。同样，有些字段必须显示指定才生效，不符合直觉。
```toml
# 目录配置
[params.page.toc]
  # 是否使用目录
  enable = true
  # 是否保持使用文章前面的静态目录
  # 必须显示指定！
  keepStatic = false
  # 是否使侧边目录自动折叠展开
  auto = false
```
2. [某些日期导致文章不显示](https://github.com/HEIGE-PCloud/DoIt/issues/926)：原因是 date 写成了未来的一天，hugo 默认不会发布该文章。需要修改 hugo 配置，与主题无关，可改用下方命令启动服务器
```bash
hugo server --disableFastRender --buildFuture
```
3. [文章上方的作者头像是否支持修改？](https://github.com/HEIGE-PCloud/DoIt/issues/920)：文档里有相应字段，但实际上不支持修改
