---
title: "命令行计算 md5、sha256"
date: 2023-05-04T21:48:12+08:00
lastmod: 2023-05-04T22:03:20+08:00
categories: ["工具"]
tags: ["命令行"]
summary: "备忘"
---

以下命令针对 macOS 平台，可能与 Linux 平台命令不同

计算 md5：

```bash
md5 filename
```

计算 sha256：

```bash
shasum -a 256 filename
```

注：
- `-a` 是 `--algorithm` 的简写，在其后指定算法，可以是 `1 (default), 224, 256, 384, 512, 512224, 512256`
- shasum 命令的详细说明可以通过 `shasum -h` 查看
