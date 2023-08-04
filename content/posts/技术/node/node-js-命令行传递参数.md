---
title: "Node.js 命令行传递参数"
date: 2023-08-04T12:05:37+08:00
lastmod: 2023-08-04T12:08:19+08:00
categories: ["技术"]
tags: ["Node.js"]
summary: ""
---

[Node.js 从命令行接收参数](https://dev.nodejs.cn/learn/nodejs-accept-arguments-from-the-command-line/)

`process.argv` 就是参数数组，前两个元素分别是 `node` 和文件路径，后面的元素是用户传入的参数。所以如果只传一个参数，可以用 `process.argv[2]` 读取
