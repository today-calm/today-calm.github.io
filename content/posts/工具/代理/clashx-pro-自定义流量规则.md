---
title: "ClashX Pro 自定义流量规则"
date: 2023-08-01T12:20:27+08:00
lastmod: 2023-08-01T12:41:02+08:00
categories: ["工具"]
tags: ["代理"]
summary: ""
---

参考 [如何自定义流量规则 #636](https://github.com/yichengchen/clashX/discussions/636#discussioncomment-4374541)

如果直接修改现有的配置规则，在规则更新时会覆盖掉我们的修改内容，所以要换一种方式来自定义我们的规则

新版 ClashX Pro 中提供了该功能：菜单栏-配置-更多设置-通用-忽略这些主机与域的代理设置

在输入框中输入要直连的域名（用逗号分隔）

```
192.168.0.0/16,10.0.0.0/8,172.16.0.0/12,127.0.0.1,localhost,*.local,timestamp.apple.com,sequoia.apple.com,seed-sequoia.siri.apple.com
```

重启 ClashX Pro

打开 ClashX Pro 的控制台，点击「日志」或「连接」选项

浏览器打开网页，在 ClashX Pro 中查看相应域名是否变为直连状态
