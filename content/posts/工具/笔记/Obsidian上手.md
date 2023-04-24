---
title: "Obsidian上手"
date: 2023-04-19T22:15:14+08:00
lastmod: 2023-04-25T01:38:05+08:00
categories: ["工具"]
tags: ["笔记"]
summary: "Markdown 笔记软件 Obsidian 的使用方法"
---

官方帮助文档：[Obsidian Help](https://help.obsidian.md/Obsidian/Index)

## 特点

优点：
- 注重隐私：完全本地
- 易迁移：目录结构简单
- 程序员友好：软件架构舒适，支持插件
- 双链笔记：链接是一等公民
- 社区庞大

缺点：
- 编辑体验不好：
    - 使用严格的 markdown 语法，需要手动处理换行，[官方似乎没有优化的打算](https://forum.obsidian.md/t/forcing-a-newline-hard-line-break-carriage-return-canonical/8789)
    - 部分快捷键缺失
- 官方同步价格较贵，需要自己搞定同步方案

## 核心概念

- vault：即整个仓库，不同 vault 的文件、配置完全隔离  
- .obsidian 目录：存放个人配置、插件
- .trash 目录：存放删除文件  
- metadata：即 front matter，使用 YAML 格式
- 2 种视图：阅读视图、编辑视图  
- 2 种模式：源码模式、实时预览

注：
- 如果想复用某个 vault 的配置，[直接拷贝 .obsidian 目录即可](https://help.obsidian.md/How+to/Working+with+multiple+vaults#Transfer+settings+between+vaults)
- 使用 git 时，[建议忽略 .obsidian/workspace 文件](https://help.obsidian.md/Advanced+topics/How+Obsidian+stores+data#Per+vault+data)

## 基础使用

- link 其他文章：连续输入两个 `[`  
- [可灵活修改附件存放位置](https://help.obsidian.md/How+to/Manage+attachments#Change+default+attachment+location)  
- 快速打开文件（内置插件）
- 命令面板（内置插件）：快速执行命令  
- [文件恢复](https://help.obsidian.md/Plugins/File+recovery#Recover+a+snapshot)（内置插件）：每隔一段时间自动生成快照
- 模板（内置插件）：可在文章任何位置插入，功能较弱，用 Templater 插件代替
- 切换阅读视图/编辑视图

注：  
- 文件恢复插件的快照存放位置不在 vault 里，而在 `/Users/yourusername/Library/Application Support/obsidian` 下，应该是以二进制存储在某个目录下  

## 主题

[Minimal](https://github.com/kepano/obsidian-minimal)，需配合 Minimal Theme Settings 插件使用

## 社区插件

- Minimal Theme Settings：上面已经提到了。  
- [Templater](https://github.com/SilentVoid13/Templater)：强大的模板语法，有许多内置函数。可使用模板创建文件，适合自动创建 front matter。
- [Linter](https://github.com/platers/obsidian-linter)：根据配置的规则格式化 md 文件，内置了许多 lint 规则，并且允许自定义正则替换规则，极大地改善了编辑体验。
- [Code Editor Shortcuts](https://github.com/timhor/obsidian-editor-shortcuts)：提供了类似 VSCode 的快捷键，弥补 Obsidian 快捷键不足的缺点。

Templater 和 Linter 两个插件稍微复杂一点，下面介绍一下 Linter 的使用，Templater 在之后结合 Hugo 发布文章时再介绍。

### Linter 插件

配置 lint 的快捷键，对应命令为  `Linter: Lint the current file`

开启规则：
- `Two Spaces Between Lines with Content`：在需要换行的行末添加两个空格。不需要手动处理换行了，真好

注：
- 不要同时使用功能有重合的规则，例如 `Paragraph blank lines` 和 `Two Spaces Between Lines with Content` 不要同时使用，[会造成功能异常](https://github.com/platers/obsidian-linter/issues/570)。
