---
title: "Hugo、Obsidian 结合实践"
date: 2023-04-22T23:35:23+08:00
lastmod: 2023-07-23T13:11:21+08:00
categories: ["工具"]
series: ["使用 Hugo 与 Obsidian 搭建博客"]
series_weight: 2
tags: ["Hugo", "Obsidian"]
summary: "使用 Obsidian 和 Hugo 快速发布一篇文章"
---

## 背景

我的笔记软件是 Obsidian，博客发布工具是 Hugo，在不做任何配置的情况下，发布一篇文章是比较麻烦的。

流程一般是：在 Obsidian 中写完文章后，将修改的文章覆盖拷贝到 Hugo content/posts/ 下，再发布。这样做有几个问题：
- 需要多执行一次 cp 命令
- 需要记住修改了哪个文件，有心智负担。或者可以将整个目录全量拷贝，但如果文章数较多，这么做显然不够优雅
- 无法在用 Obsidian 编辑文章时实时预览 Hugo 页面效果  

因此，最好能直接使用 content/posts/ 目录作为 Obsidian vault。经过尝试，发现这种方式是可行的，不过需要做一些配置。

## 忽略 Obsidian 文件

Hugo 项目在创建时就使用了 git 管理，所以首先要配置的是 .gitignore，Obsidian 的配置文件是没有必要上传到 GitHub 的。

```bash
# .gitignore
.obsidian/
.trash/
```

其次要做的是让 Hugo 在渲染时忽略 Obsidian 配置文件，Hugo 提供了 2 种忽略文件的方法：
- [Ignore Content and Data Files when Rendering](https://gohugo.io/getting-started/configuration/#ignore-content-and-data-files-when-rendering)
- [Module Config: mounts](https://gohugo.io/hugo-modules/configuration/#module-config-mounts)  

官方更推荐 mounts 的方式，我这里也采用 mounts 方式，配置如下：
```toml
# 配合 Obsidian，需要对目录重新映射
[module]
# 渲染时忽略 obsidian 文件
# 经测试，点开头的目录不会被打包，不用添加到这里了
# glob 与正则写法不同。若要忽略多个目录，需要写成 {folder1,folder2}，注意逗号后不能有空格
[[module.mounts]]
  excludeFiles = 'posts/{template}/*'
  source = 'content'
  target = 'content'
```

## 使用图片资源

**更新：以下方法还是比较复杂，不推荐使用了，更优的方法可以参考我的另一篇文章 [用 Hugo Render Hooks 简化 Markdown 中链接、图片的引用](4-用-hugo-render-hooks-简化-markdown-中链接-图片的引用.md)**

---

这部分折腾了很久，原因是 Hugo 不兼容 Obsidian 生成的链接格式。Obsidian 使用相对路径或绝对路径引用资源，在 Hugo 中都无法展示图片。于是搜索了下怎么解决。

首先，DoIt 主题介绍了 [3 种使用资源的方式](https://hugodoit.pages.dev/zh-cn/theme-documentation-content/#contents-organization)，但这几种方式需要更改目录结构，或将资源放到 Obsidian vault 以外的地方，都不合适。

接着去了解了 Hugo 建议的使用资源方式：
1. 将资源放在靠近 md 的位置  
需要将目录结构改造成 [Page Bundles](https://gohugo.io/content-management/page-bundles/)、[Page Resources](https://gohugo.io/content-management/page-resources/) 中的结构，md 文件命名为 index.md，[这里有个例子](https://github.com/gohugoio/hugo/issues/1240#issuecomment-753077529)。这种方式破坏了结构，我肯定不能接受。
2. 把资源都放到 static 目录下  
参考 [Static Files](https://gohugo.io/content-management/static-files/)  
(1) 可以通过配置 staticDir 使用多个目录，那就可以尝试把 content/posts/assets 作为 static files。  
(2) 另外，在搜索时发现有人通过 [mount static 目录](https://discourse.gohugo.io/t/getting-images-to-display-in-hugo-from-attachments-sub-directory/41744) 的方式使用资源，可以尝试下。

由于官方更推荐用 mount，那就用这种方法试下。

配置 Obsidian：设置插入资源链接时使用绝对路径  
配置 Hugo：
```toml
[module]
[[module.mounts]]
  # glob 与正则写法不同。若要忽略多个目录，需要写成 {folder1,folder2}，注意逗号后不能有空格
  excludeFiles = 'posts/{template,assets}/*'
  source = 'content'
  target = 'content'
[[module.mounts]]
  source = 'content/posts/assets'
  target = 'static/assets'
```

这时发现 Hugo 还是不展示图片，原因是 Obsidian 插入图片的链接格式为 `assets/xxx.png`，Hugo 不兼容该写法，必须要在最前面加上 `/`。

注：为了让 Obsidian 自动给链接加上 `/`，可以使用 Linter 插件，配置 Custom Regex Replacement

改好后看起来 ok 了，但引入了个新问题：mount 后，原先的 static 目录被替换掉了，而 favicon 在原来的 static 目录下，mount 后就无法显示了。

查了下 mount 能否 merge，[发现是可以的](https://discourse.gohugo.io/t/how-to-merge-and-not-overwrite-hugo-mount-directories/29819)，配置如下：
```toml
[[module.mounts]]
  source = 'static'
  target = 'static'
[[module.mounts]]
  source = 'content/posts/assets'
  target = 'static/assets'
```

## 自动生成并更新 front matter

Hugo 在渲染文章时需要用到 front matter 中的信息，手动创建太麻烦了，最好能用模板自动创建。

下面借助 2 个 Obsidian 插件实现自动生成并更新 front matter：
- Templater（插入 front matter）
- Linter（更新 front matter）

首先介绍一下 Templater 的使用

### Templater 插件

概念：
- template：模板文件
- command：`<% xxx %>` 的结构，类似表达式
- function：就是 JS 的函数，分为 internal functions（预置函数，如 `tp.date.now()`） 和 user functions（自定义）
- user functions：分为 Script User Functions 和 System Command User Functions

特殊的 command：
- Dynamic Commands：以 `<%+` 开头，github issue 中作者说以后会移除该功能，不推荐使用
- Javascript Execution Command：以 `<%*` 开头，可执行 JS 代码
- Whitespace Control：包括 `<%_`、`_%>`、`<%-`、`-%>`，用于去除模板留下的空行

配置：
- 指定模板目录
- 新建文件时使用模板：指定启用该功能的目录，并选择模板

一般在创建文件时使用模板就够了，如果想手动插入模板，也可以，在命令面板中输入 `Templater: Open Insert Template Modal` 即可。

当前我的模板是：
```toml
---
title: ""
date: <% tp.file.creation_date("YYYY-MM-DD[T]HH:mm:ss[+08:00]") %>
lastmod: 
categories: ["<% tp.file.folder(true).split('/')[0] %>"]
tags: [""]
summary: ""
---


```

接下来介绍下如何用 Linter 更新 front matter

### Linter 插件

配置：
- `YAML Timestamp`：用当前文件的最后修改日期更新 Date Modified Key。为了和 Hugo 的 key 一致，这里把 Date Modified Key 配置成 `lastmod`

注：
- [Date Modified 使用当前时间而非最后修改时间](https://github.com/platers/obsidian-linter/issues/628) 不是 bug，而是正常的设计逻辑。因为如果当前文件中 Date Modified 的值不正确，使用最后修改时间更新 Date Modified，会导致文件的修改时间变化，这样每次 lint 都会更新，无限循环。使用当前时间即可解决这个问题，有一点误差问题不大。
- 我的 front matter 使用了 TOML 格式（和 DoIt 主题保持一致），但该插件的一些配置是针对 YAML 的，所以不确定是否出问题。另外，Obsidian 的 front matter 只支持 YAML、JSON。

## 最后

最后，感谢以下文章引起了我折腾的兴趣，也为我提供了一些自动化的思路
- [Obsidian体验与新工作流](https://www.atksoto.com/blog/legacy/Obsidian-0)  
- [Obsidian，Hugo](https://www.atksoto.com/blog/202301/Obsidian-Hugo)  
- [obsidian 配合 hugo、cloudflare：让发布博客简单到不可思议](https://lillianwho.com/posts/obsidian-hugo-cloudflare/)
