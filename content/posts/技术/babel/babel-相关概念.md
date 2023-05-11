---
title: "Babel 相关概念"
date: 2023-03-12T21:03:33+08:00
lastmod: 2023-04-30T21:33:36+08:00
categories: ["技术"]
tags: ["Babel"]
summary: ""
---

本文参考了以下文章，记录 Babel 的相关概念
- [不容错过的 Babel7 知识](https://juejin.cn/post/6844904008679686152)
- [关于babel的详细解读(精华又通俗)](https://juejin.cn/post/6844904199554072583)  
- [corejs 官方文档](https://github.com/zloirock/core-js/blob/master/docs/zh_CN/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md)

## syntax 和 api

Babel 把高版本代码分为两类：
- syntax（let、class、arrow function...）
- api（Promise、includes...）

## preset 和 plugin

preset：plugin/preset 的集合，如 @babel/preset-env  
plugin：支持单个功能，如 @babel/plugin-transform-arrow-functions（转换箭头函数）

[babel 官网](https://www.babeljs.cn/docs/plugins#%E6%8F%92%E4%BB%B6%E9%A1%BA%E5%BA%8F) 给出了两者的执行顺序：
> -   插件在 Presets 前运行。
> -   插件顺序从前往后排列。
> -   Preset 顺序是颠倒的（从后往前）。

## babel 配置文件

.babelrc 或 babel.config.js 或其他格式

## @babel-core

babel 核心模块

## @babel-cli

命令行工具

## @babel/preset-env

- 属于 preset，内置了很多插件。  
- 只处理 syntax，不处理 api。  
- 可通过 target 字段或 .browserslistrc 指定目标环境，如果不指定目标环境，会增加不必要的兼容，造成体积过大。

[useBuiltIns](https://babeljs.io/docs/babel-preset-env.html#usebuiltins)：用于自动去除多余的 polyfill，具体介绍参考 [useBuiltIns: entry with corejs: 3](https://github.com/zloirock/core-js/blob/master/docs/zh_CN/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md#usebuiltins-entry-with-corejs-3)，值可以设置为：
- false（默认值，不处理 import corejs 语句，即全量引入 polyfill）
- entry（替换 corejs entry，只保留目标环境不支持的 polyfill）
- usage（在每个文件的开头引入目标环境不支持、仅在当前文件中使用的 polyfill）  
使用 useBuiltIns 时需要将 corejs 字段加上，推荐用 3 版本

## @babel-polyfill（废弃）

在 Babel > 7.4.0 的版本后已不推荐使用（没有升级 core-js 3），替换方法：
```js
// 原来
import "@babel/polyfill";

// 现在
import "core-js/stable";
import "regenerator-runtime/runtime";
```
处理 api 的转换，是运行时依赖，不是 devDependencies。  
包括两部分：
- core-js
- regenerator-runtime  
需要在代码中 import '@babel-polyfill'

core-js 有 2、3 两个版本，2 不再维护，现在推荐用 3（3 支持 polyfill 实例方法）

## @babel/plugin-transform-runtime

作用有 2 个：
- 防止重复注入 Babel 的辅助函数，减小代码体积
- 创建沙盒环境，防止污染全局作用域  

适用于开发类库

[@babel/plugin-transform-runtime 到底是什么？](https://zhuanlan.zhihu.com/p/147083132)  
评论区有讨论，作者说的不对，useBuiltIns 和 @babel/plugin-transform-runtime 解决的是不同的问题，不能混用 https://github.com/babel/babel/issues/10271#issuecomment-528379505

## @babel-runtime

https://babeljs.io/docs/babel-runtime  
配合 @babel/plugin-transform-runtime 使用
> `@babel/runtime` is just the package that contains the implementations of the functions in a modular way.
