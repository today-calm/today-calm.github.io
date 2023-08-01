---
title: "GeoIP 是什么"
date: 2023-08-01T12:49:14+08:00
lastmod: 2023-08-01T12:59:57+08:00
categories: ["工具"]
tags: ["代理"]
summary: ""
---

参考 [GeoIP 简介](https://blog.csdn.net/zzhongcy/article/details/121247674)、[GeoIP2 · CN](https://github.com/Hackl0us/GeoIP2-CN#readme)

在使用 ClashX Pro 时，控制台内看到了某些域名使用了 `GeoIP :: CN` 的规则，可以直连。为什么这个规则可以直连？一般不是按照域名匹配的吗？

因为现有的配置规则文件中有这样一个规则：

```
- GEOIP,CN,直连
```

在域名无法匹配时，会通过 GeoIP 数据库查询该 ip 是否是国内机器，由此控制是否直连

因此，域名并不是唯一能使用的规则，还可以用 ip 等方式来控制流量
