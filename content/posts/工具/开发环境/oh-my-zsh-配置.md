---
title: "Oh My Zsh 配置"
date: 2022-11-26T17:29:33+08:00
lastmod: 2023-05-02T13:04:13+08:00
categories: ["工具"]
tags: ["开发环境", "zsh"]
summary: ""
---

主要参考 [https://gist.github.com/kevin-smets/8568070](https://gist.github.com/kevin-smets/8568070)

安装步骤：[https://github.com/ohmyzsh/ohmyzsh#basic-installation](https://github.com/ohmyzsh/ohmyzsh#basic-installation)

重新加载 .zshrc 的方法：`exec zsh`​​ ，不建议用 `source .zshrc`​​，FAQ 里有写

## alias

[注意单引号和双引号不同，单引号就是字面量，双引号会替换其中的变量](https://stackoverflow.com/questions/6697753/difference-between-single-and-double-quotes-in-bash)

```bash
alias cl='clear'
alias l='ls -G'
alias la='ls -Ah'
alias ll='ls -lh'
alias lla='ls -lhA'

alias gs='git status'
alias ga='git add'
alias gc='git commit -m'
alias gca='git commit --amend'
alias gl='git log'
alias glo='git log --oneline'
alias glg='git log --oneline --decorate --graph --all'
alias gb='git branch'
alias gco='git checkout'

alias y='yarn'
alias ys='yarn start'
alias yd='yarn dev'
alias rmm='rm -rf node_modules'
```

## theme

安装 powerline10k：[https://github.com/romkatv/powerlevel10k#getting-started](https://github.com/romkatv/powerlevel10k#getting-started)

* 安装推荐字体
* p10k configure

隐藏 git prompt：在 .p10k.zsh 里找到 `POWERLEVEL9K_LEFT_PROMPT_ELEMENTS`​，将 `vcs`​ 注释掉

## plugin

在 .zshrc 中找到 `plugins`，将自带的 git plugin 删掉（防止与自定义的 git alias 冲突）

安装：
- [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md#oh-my-zsh)
- [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md#oh-my-zsh)

## 其他

禁用自动更新

```bash
# disable automatic updates
zstyle ':omz:update' mode disabled
```
