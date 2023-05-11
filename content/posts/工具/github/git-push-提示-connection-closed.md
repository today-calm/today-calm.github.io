---
title: "git push 提示 Connection Closed"
date: 2023-05-03T21:16:04+08:00
lastmod: 2023-05-03T21:43:26+08:00
categories: ["工具"]
tags: ["GitHub", "代理"]
summary: "kex_exchange_identification: Connection closed by remote host 错误的解决方法"
---

今天 git push 的时候报了这个错误：

```txt
kex_exchange_identification: Connection closed by remote host
Connection closed by 198.xxx.xxx.xxx port 22
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

很奇怪，我最近也没改什么东西呀？搜索了下，发现有朋友遇到类似的问题
- [SSH通过443端口连接GitHub](https://zhutmost.com/Engineering/github-ssh-443/)
- [SSH 如何通过代理？](https://www.v2ex.com/t/870088)

原因是我们的代理工具的 22 端口被防火墙屏蔽

解决方法是换一个端口连接 SSH，GitHub 有专门的文档讲解如何配置 [在 HTTPS 端口使用 SSH](https://docs.github.com/zh/authentication/troubleshooting-ssh/using-ssh-over-the-https-port)

没想到 443 端口也能建立 SSH 连接...

简单描述一下步骤：
1. 使用 `ssh` 命令测试 443 端口能否连接 GitHub
2. 如果能够连接，那么修改或创建 `~/.ssh/config` 文件，配置别名、端口
3. 再次测试连接是否正常

注：443 端口的域名是 `git@ssh.github.com`，而非 `github.com`

配置好后，再次 git push！

```txt
kex_exchange_identification: Connection closed by remote host
Connection closed by 198.xxx.xxx.xxx port 443
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

额，怎么还是报错？不过变成 443 端口报错了

正常来说不应该这样，重新执行一下 `ssh -T git@github.com` 看看

```txt
Hi today-calm! You've successfully authenticated, but GitHub does not provide shell access.
```

这也没问题啊，再 git push 试一下，这次成功了。好奇怪，难道这也有缓存？
