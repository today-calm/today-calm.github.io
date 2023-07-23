---
title: "使用 giscus 作为博客评论系统"
date: 2023-05-02T17:03:53+08:00
lastmod: 2023-07-23T13:11:19+08:00
categories: ["工具"]
series: ["使用 Hugo 与 Obsidian 搭建博客"]
series_weight: 3
tags: ["Hugo", "giscus"]
summary: "我的博客目前还差评论功能，本文利用 giscus 来添加评论功能。"
---

我的博客目前还差评论功能，本文利用 giscus 来添加评论功能。

## 选择评论系统

我首先搜索了下评论系统有哪些，[Hugo 官网](https://gohugo.io/content-management/comments/#alternatives) 有推荐，[DoIt 主题的支持列表](https://github.com/HEIGE-PCloud/DoIt#social-and-comment-systems) 里也有列举一部分

本着能不折腾就不折腾的原则，我选择了 [giscus](https://github.com/giscus/giscus)，原因是：
- 无需数据库
- 开源
- 还在维护

giscus 其实参考了 [utterances](https://github.com/utterance/utterances)，utterances 使用 GitHub Issues 存放评论，giscus 使用 GitHub Discussions 存放评论。但 utterances 貌似更新不太频繁了，而且功能没有 giscus 完善（比如支持 self-hosted），所以选择 giscus 就行啦。

## giscus 工作原理

官方 README 里写的很清楚了：

> When giscus loads, the [GitHub Discussions search API](https://docs.github.com/en/graphql/guides/using-the-graphql-api-for-discussions#search) is used to find the Discussion associated with the page based on a chosen mapping (URL, `pathname`, `<title>`, etc.). If a matching discussion cannot be found, the giscus bot will automatically create a discussion the first time someone leaves a comment or reaction.
> 
> To comment, visitors must authorize the [giscus app](https://github.com/apps/giscus) to [post on their behalf](https://docs.github.com/en/developers/apps/identifying-and-authorizing-users-for-github-apps) using the GitHub OAuth flow. Alternatively, visitors can comment on the GitHub Discussion directly. You can moderate the comments on GitHub.

翻译一下就是：
- 根据设置的 mapping 规则（如 pathname）查找对应的 GitHub Discussions，如果没找到，就创建一个 discussion
- 在博客里评论时需要通过 GitHub OAuth 流程验证，或者也可以直接到 GitHub Discussions 里评论

## 接入 giscus

访问 [https://giscus.app/zh-CN](https://giscus.app/zh-CN)，查看配置小节，会引导你配置 giscus，配置完成后会生成如下的脚本

```html
<script src="https://giscus.app/client.js"
    data-repo="[在此输入仓库]"
    data-repo-id="[在此输入仓库 ID]"
    data-category="[在此输入分类名]"
    data-category-id="[在此输入分类 ID]"
    data-mapping="pathname"
    data-strict="0"
    data-reactions-enabled="1"
    data-emit-metadata="0"
    data-input-position="bottom"
    data-theme="preferred_color_scheme"
    data-lang="zh-CN"
    crossorigin="anonymous"
    async
</script>
```

由于 DoIt 主题已经默认支持了 giscus，直接在 config.toml 中配置相应字段即可

```toml
[params.page.comment]
  enable = true
  [params.page.comment.giscus]
    enable = true
    # owner/repo
    dataRepo = ""
    dataRepoId = ""
    dataCategory = ""
    dataCategoryId = ""
    dataMapping = "pathname"
    dataReactionsEnabled = "1"
    dataEmitMetadata = "0"
    dataInputPosition = "bottom"
    lightTheme = "light"
    darkTheme = "dark"
    dataLang = "zh-CN"
```

注：本地 Hugo 调试时，默认不会使用评论系统，[需要指定运行环境为 production 才能看到效果](https://hugodoit.pages.dev/zh-cn/theme-documentation-basics/#site-configuration)

> `hugo serve` 的默认运行环境是 `development`, 而 `hugo` 的默认运行环境是 `production`.  
> 
> 由于本地 `development` 环境的限制, **评论系统**, **CDN** 和 **fingerprint** 不会在 `development` 环境下启用.  
> 
> 你可以使用 `hugo serve -e production` 命令来开启这些特性.

## 一些可能遇到的问题

1. pathname 改变导致匹配不到评论  
   在 GitHub Discussions 中修改 discussion 标题即可

2. 匹配的是其他文章的评论  
   因为 GitHub 搜索使用的是模糊匹配，不是严格匹配。我暂时没有遇到这个问题，如果遇到了可以开启 [“使用严格的标题匹配”](https://github.com/giscus/giscus/blob/main/ADVANCED-USAGE.md#data-strict)
