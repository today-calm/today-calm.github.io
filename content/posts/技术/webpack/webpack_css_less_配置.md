---
title: "Webpack CSS、Less 配置"
date: 2023-04-02T16:33:29+08:00
lastmod: 2023-04-30T21:42:57+08:00
categories: ["技术"]
tags: ["Webpack"]
summary: "本文是 [A mostly complete guide to webpack 5 (2020)](https://www.valentinog.com/blog/webpack/#entry-point) 的学习笔记，主要关注 Css、Less 的处理"
---

本文是 [A mostly complete guide to webpack 5 (2020)](https://www.valentinog.com/blog/webpack/#entry-point) 的学习笔记，主要关注 CSS、Less 的处理

## css 配置

### 基本使用

首先安装 style-loader、css-loader
```bash
yarn add -D css-loader style-loader
```

css-loader 的作用：输入 css 文件，输出 js 模块  
style-loader 的作用：输入 js 模块，输出 js。在运行时，向 DOM 注入 style 标签  
但具体机制其实不太明白，官方文档和 stackOverflow 讲得都不清楚  

配置 webpack.config.js
```js
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      }
    ]
  },
```

注：loader 是从后向前执行，先使用 css-loader，后使用 style-loader  

打包，可以发现在 DOM 中插入了 style 标签
```html
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Webpack 学习</title>
    <script defer="" src="index.js"></script>
    <style>h1 { color: red; }</style>
  </head>
  
  <body>
    <h1>这是一个 h1 标签</h1>
  </body>
</html>
```

### 区分开发、生产环境

css-loader 推荐配置：
- 在 production 环境将 css 从 bundle js 中抽出来，因为可以与 js 并行加载，需要使用 MiniCssExtractPlugin
- 在 development 环境使用 style-loader，因为在 hmr 的时候，注入 style 比抽出 css 的效率更高  

注：MiniCssExtractPlugin 与 style-loader 不可同时使用

使用 MiniCssExtractPlugin
```js
  plugins: [
    new MiniCssExtractPlugin(),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      }
    ]
  },
```

查看 DOM，发现插入了 link 标签，请求中也多了 index.css
```html
<link href="index.css" rel="stylesheet">
```

MiniCssExtractPlugin 文档说明：
> It creates a CSS file per JS file which contains CSS.

推荐配置如下，区分了 development 和 production
```js
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const devMode = process.env.NODE_ENV !== "production";

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          devMode ? "style-loader" : MiniCssExtractPlugin.loader,
          "css-loader",
        ],
      },
    ],
  },
  plugins: [].concat(devMode ? [] : [new MiniCssExtractPlugin()]),
};
```

注意要在 package.json scripts 中配置 NODE_ENV 环境变量
```json
"dev": "NODE_ENV=development webpack --mode development",
```

## less 配置

安装 less、less-loader
```bash
yarn add -D less less-loader
```

webpack.config.js
```js
module: {
    rules: [
      {
        test: /\.less$/,
        use: [
          isDev ? 'style-loader' : MiniCssExtractPlugin.loader,
          'css-loader',
          {
            loader: 'less-loader',
            options: {
              lessOptions: {
                strictMath: true,
              }
            }
          },
        ],
      }
    ]
  },
```
