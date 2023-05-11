---
title: "macOS 前端开发环境搭建"
date: 2022-11-26T17:29:33+08:00
lastmod: 2023-05-11T08:50:43+08:00
categories: ["工具"]
tags: ["开发环境"]
summary: ""
---

## Git

在终端输入 `git --version`，会提示通过 xcode 安装

配置用户名、邮箱

```bash
git config --global user.name "xxx"
git config --global user.email "xxx@yyy.com"
```

## VSCode

单独记录，见 [VSCode 配置](vscode-配置.md)

## homebrew

官网：[https://brew.sh/](https://brew.sh/)

执行官网的安装命令，报错：

```bash
curl: (7) Failed to connect to raw.githubusercontent.com port 443 after 12 ms: Connection refused
```

如果你会魔法，应该可以解决这个问题。否则可以尝试下 Gitee 上的镜像：
- [MacBook使用笔记：安装Homebrew（M1）](https://zhuanlan.zhihu.com/p/372576355)
- [https://gitee.com/cunkai/HomebrewCN](https://gitee.com/cunkai/HomebrewCN)

安装完成后，将环境变量写入 .zshrc

```bash
# 这是 m1 的路径，和 intel 不一样
eval $(/opt/homebrew/bin/brew shellenv)
```

[禁止 brew install 时更新](https://apple.stackexchange.com/questions/293250/how-to-install-an-homebrew-formula-without-updating-homebrew-itself) 也写入 .zshrc

```bash
export HOMEBREW_NO_AUTO_UPDATE=1
```

## iterm2

官网：[https://iterm2.com/](https://iterm2.com/)

安装：在官网下载 zip 包，解压后拖入 Application

配置：
- general-services：取消勾选 `check for updates automatically`
- appearance - general - status bar location：`bottom`
- profiles：
    - colors：从 [https://iterm2colorschemes.com](https://iterm2colorschemes.com) 下载主题，在 color presets 中 import
    - terminal：`scrollback lines` 调成 100,000
    - session：勾选 `status bar enabled`
    - keys - key mappings：
        - presets 里选择 `natural text editing`
        - [Remapping ⌘-. (command-period) to BREAK in iTerm2](https://blog.andrewmadsen.com/2020/01/25/remapping-commandperiod-to.html)：增加一个新快捷键，action 选择 `send hex code`，keyboard shortcut 录入 `cmd + .`​，最下面填 0x03
- keys - hotkey：勾选 `show/hide all windows with a system-wide hotkey`

## oh-my-zsh

单独记录，见 [Oh My Zsh 配置](oh-my-zsh-配置.md)

## nvm

### 安装

[https://github.com/nvm-sh/nvm#installing-and-updating](https://github.com/nvm-sh/nvm#installing-and-updating)

注：
- 官方不建议通过 homebrew 安装，README 有写
- node >= 16.0 才正式支持 m1 芯片，安装默认就是 arm64 架构（可以看包名是否带有 arm64）
- node < 16.0 需要用特殊的安装方法：在 README 中搜索 Macs with M1 chip 小节，有具体讲怎么安装。简单说就是要在 Rosetta terminal 中安装 x86_64 版本。如果用普通方法安装 < 16.0 的 node，可能编译报错或内存不足。

### npm 换源

如果需要换源，可以使用下方命令（以淘宝源为例）：

```bash
# 设置源
npm config set registry https://registry.npmmirror.com

# 查看当前源
npm config get registry
```

注：淘宝源的域名改了 [【望周知】淘宝 NPM 镜像站喊你切换新域名啦](https://zhuanlan.zhihu.com/p/430580607)

### 安装 yarn

安装 yarn v1 版本

```bash
npm install -g yarn
```

注：yarn v3 版本的安装可参考 [https://yarnpkg.com/getting-started/install](https://yarnpkg.com/getting-started/install)，和 yarn v1 区别挺大的，具体区别见 [https://yarnpkg.com/getting-started/migration](https://yarnpkg.com/getting-started/migration)  
‍