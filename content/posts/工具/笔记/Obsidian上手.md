---
title: "Obsidian上手"
date: 2023-04-19T22:15:14+08:00
lastmod: 2023-04-23T01:18:20+08:00
categories: ["工具"]
tags: ["笔记"]
summary: "Markdown 笔记软件 Obsidian 的使用方法"
featuredImage: "/assets/截屏2023-04-23%2000.58.01.png"
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
- 官方同步价格较贵，需要自己搞定同步方案
- 部分快捷键缺失（向上新建空行、插入表格）

## 核心概念

- vault：即整个仓库，不同 vault 的文件、配置完全隔离  
- .obsidian 目录：存放个人配置、插件
- .trash 目录：删除文件存放位置  
- metadata：即 front matter，使用 YAML 格式
- 2 种视图：阅读视图、编辑视图  
- 2 种模式：源码模式、实时预览

注：
- 如果想复用某个 vault 的配置，[直接拷贝 .obsidian 目录即可](https://help.obsidian.md/How+to/Working+with+multiple+vaults#Transfer+settings+between+vaults)
- 使用 git 时，[建议忽略 .obsidian/workspace 文件](https://help.obsidian.md/Advanced+topics/How+Obsidian+stores+data#Per+vault+data)

## 基础使用

- link 其他文章：连续输入两个 `[`  
- [可灵活修改附件存放位置](https://help.obsidian.md/How+to/Manage+attachments#Change+default+attachment+location)  
- 命令面板（内置插件）：快速执行命令  
- [文件恢复](https://help.obsidian.md/Plugins/File+recovery#Recover+a+snapshot)（内置插件）：每隔一段时间自动生成快照
- quick switcher（内置插件）：快速打开文件
- [模板](https://help.obsidian.md/Plugins/Templates)（内置插件）：类似 snippet，可在文章任何位置插入
- 按住 option 可增加光标
- 可配置快捷键切换阅读视图/编辑视图

注：
- 文件恢复插件的快照存放位置不在 vault 里，而在 /Users/yourusername/Library/Application Support/obsidian 下，应该是以二进制存储在某个目录下  
- 模板插件的功能较弱，用 Templater 插件代替

## 主题

[Minimal](https://github.com/kepano/obsidian-minimal)，需配合 Minimal Theme Settings 插件使用  
该插件中内置了多种主题，我选择了 Notion

## 社区插件

- Minimal Theme Settings：上面已经提到了  
- [Obsidian Hard Breaks](https://github.com/bkis/obsidian-hard-breaks)：obsidian 默认是软换行，在预览时会发现没有换行，需要在行末加两个空格或反斜线来换行，该插件提供了格式化的命令。但该插件貌似无法识别链接的换行。
- [Templater](https://silentvoid13.github.io/Templater/)：强大的模板语法，内置了许多有用的函数，并且能够执行 JS

下面单独介绍一下 Templater 的使用

### Templater 插件使用

[https://github.com/SilentVoid13/Templater](https://github.com/SilentVoid13/Templater)

概念：
- template：模板文件
- command：`<% xxx %>` 的结构，类似表达式
- function：就是 JS 的函数，分为 internal functions（预置函数，如 `tp.date.now()`） 和 user functions（自定义）
- user functions（未详细了解）：分为 Script User Functions 和 System Command User Functions

特殊的 command：
- Dynamic Commands：以 `<%+` 开头，github issue 中作者说以后会移除该功能，不推荐使用
- Javascript Execution Command：以 `<%*` 开头，可执行 JS 代码，不详细了解了
- Whitespace Control：包括 `<%_`、`_%>`、`<%-`、`-%>`，用于去除模板留下的空行

使用：
- 指定模板目录
- 新建文件时使用模板：不太好用，新建文件时文件名是 Untitled，front matter 中的 title 也是 Untitled。不如手动插入。
- 手动插入模板：在 Hotkeys 中找到 Templater: Open Insert Template Modal，配置快捷键

下面是我的 front matter 模板（字段均与 hugo 有关，在此无需关注）

```yaml
---
title: "<% tp.file.title %>"
date: <% tp.file.creation_date("YYYY-MM-DD[T]HH:mm:ss[+08:00]") %>
categories: ["<% tp.file.folder(true).split('/')[0] %>"]
tags: ["<% tp.file.folder(true).split('/').slice(-1) %>"]
summary: ""
---


```