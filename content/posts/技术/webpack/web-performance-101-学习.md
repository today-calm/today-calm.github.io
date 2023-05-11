---
title: "Web Performance 101 学习"
date: 2023-04-05T15:50:36+08:00
lastmod: 2023-04-30T21:20:49+08:00
categories: ["技术"]
tags: ["webpack", "性能优化"]
summary: "本文是 [Web Performance 101](https://3perf.com/talks/web-perf-101/) 学习笔记，主要关注页面的加载、渲染速度"
---

本文是 [Web Performance 101](https://3perf.com/talks/web-perf-101/) 学习笔记，主要关注页面的加载、渲染速度

## JS

- 代码压缩
- script 标签使用 async/defer，不要阻塞 DOM 解析（script 默认阻塞 DOM 解析是因为可能有 `document.write()` 之类操作 DOM 的代码，如果之后执行可能会返工）
- code splitting、dynamic import
- 移除未使用代码，如 moment.js 去除多余时区，lodash 按需加载，core-js 按需加载

## CSS

- 代码压缩
- [extract critical css](https://web.dev/extract-critical-css/)  
  问题：css 会阻塞页面渲染，设计初衷是防止无样式到有样式的闪烁，想让用户直接看到有样式的页面  
  建议：
    - 把 critical css 内联到 html 里，这样不用再单独发请求
    - 把 non-critical css 放到 head 标签外面（有工具可以 extract critical css，如 critical、penthouse）
  
 ## HTTP
- 代码压缩
- 传输数据压缩为二进制，使用 brotli 算法（能压缩 60% - 80% 体积）  
    注意只压缩文本文件（js、css、html），媒体文件不要压缩，它们已经是压缩过的二进制文件，再压缩可能体积变大  
    brotli 不支持旧浏览器（如 IE 11），旧浏览器需要使用 gzip 代替（压缩效果不如 brotli）
- 使用 CDN
- preload，有几种类型：
    - dns-prefetch：提前发起 DNS 请求，解析 ip
    - preconnect：提前与服务器建立连接，注意开销较大，不要滥用
    - prefetch：以低优先级预请求资源，适合下一页的 js
    - preload：以高优先级预请求资源，适合马上需要用的资源，如 non-critical css
    - prerender：在一个隐藏标签页中渲染页面，用户打开后会立马渲染好，google 搜索第一个结果的预加载就是这么做的  

  注意不能滥用预加载，手机用户可能不喜欢偷跑流量

## 优化工具

[webpack-bundle-analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer)
