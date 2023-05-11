---
title: "VSCode 配置"
date: 2022-11-26T17:29:33+08:00
lastmod: 2023-05-01T11:51:53+08:00
categories: ["工具"]
tags: ["VSCode", "开发环境"]
summary: ""
---

配置从终端打开 VSCode：[Launching from the command line](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line)

## extensions

- ESLint
- ES7+ React/Redux/React-Native snippets
- GitLens - Git supercharged
- JavaScript(ES6) code snippets
- Live Server
- Material Theme
- open in browser
- Prettier - Code formatter
- React Hooks Snippets
- Svg Preview
- Vetur
- vscode-icons​
- WXML - Language Service

## settings

VSCode 配置文件使用了 [jsonc](https://code.visualstudio.com/Docs/languages/json#_json-with-comments) 格式，可以加注释

```json
{
  // vscode 自身
  "update.mode": "manual",
  "workbench.colorTheme": "Material Theme Lighter High Contrast",
  "workbench.layoutControl.enabled": false,
  "workbench.sideBar.location": "right",
  "workbench.startupEditor": "none",
  "workbench.enableExperiments": false,
  "workbench.editor.untitled.hint": "hidden",
  "editor.tabSize": 2,
  "editor.fontSize": 13,
  "editor.minimap.enabled": false,
  "editor.gotoLocation.multipleDefinitions": "goto",
  "search.collapseResults": "auto",
  "extensions.autoUpdate": false,
  "extensions.autoCheckUpdates": false,
  "extensions.ignoreRecommendations": true,
  // git
  "diffEditor.ignoreTrimWhitespace": false,
  "git.showPushSuccessNotification": true,
  "git.suggestSmartCommit": false,
  "git.enableStatusBarSync": false,
  "gitlens.views.commits.files.layout": "tree",
  "gitlens.keymap": "none",
  "gitlens.views.commitDetails.files.layout": "tree",
  "gitlens.views.branches.files.layout": "list",
  "gitlens.views.repositories.branches.showBranchComparison": false,
  "gitlens.hovers.autolinks.enabled": false,
  // 终端
  "terminal.integrated.defaultProfile.osx": "zsh",
  "terminal.integrated.scrollback": 100000,
  "terminal.integrated.fontFamily": "MesloLGS NF",
  "terminal.external.osxExec": "iTerm.app",
  "terminal.explorerKind": "external",
  // 扩展
  "vsicons.dontShowNewVersionMessage": true,
  "liveServer.settings.donotShowInfoMsg": true,
  "javascript.format.semicolons": "insert",
  "typescript.format.semicolons": "insert",
  "workbench.iconTheme": "vscode-icons",
  "[javascript]": {
    "editor.defaultFormatter": "vscode.typescript-language-features"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[jsonc]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[html]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
}
```

## keyboard shortcuts

修改中断终端的快捷键：[How can I configure Cmd+. to map to Ctrl+C like macOS' built-in terminal?](https://code.visualstudio.com/docs/terminal/basics#_how-can-i-configure-cmd-to-map-to-ctrlc-like-macos-builtin-terminal)

`"gitlens.keymap": "none"` 不知道为什么无效，只能手动移除有冲突的快捷键了

```json
[
  // 添加的快捷键
  {
    "key": "cmd+t",
    "command": "workbench.action.files.newUntitledFile"
  },
  {
    "key": "cmd+n",
    "command": "workbench.action.newWindow"
  },
  {
    "key": "cmd+k p",
    "command": "copyRelativeFilePath",
    "when": "editorFocus"
  },
  {
    "key": "cmd+.",
    "command": "workbench.action.terminal.sendSequence",
    "when": "terminalFocus",
    "args": { "text": "\u0003" }
  },
  {
    "key": "cmd+k r",
    "command": "typescript.findAllFileReferences"
  },
  {
    "key": "cmd+1",
    "command": "workbench.view.explorer",
    "when": "viewContainer.workbench.view.explorer.enabled"
  },
  {
    "key": "cmd+2",
    "command": "workbench.action.findInFiles"
  },
  {
    "key": "cmd+3",
    "command": "workbench.view.scm",
    "when": "workbench.scm.active"
  },
  {
    "key": "cmd+4",
    "command": "gitlens.showCommitsView"
  },
  {
    "key": "cmd+5",
    "command": "gitlens.showSearchAndCompareView"
  },

  // 禁用的快捷键
  {
    "key": "shift+cmd+c",
    "command": "-workbench.action.terminal.openNativeConsole",
    "when": "!terminalFocus"
  },
  {
    "key": "cmd+t",
    "command": "-workbench.action.showAllSymbols"
  },
  {
    "key": "cmd+n",
    "command": "-workbench.action.files.newUntitledFile"
  },
  {
    "key": "shift+cmd+n",
    "command": "-workbench.action.newWindow"
  },
  {
    "key": "cmd+k p",
    "command": "-workbench.action.files.copyPathOfActiveFile"
  },
  {
    "key": "cmd+k r",
    "command": "-workbench.action.files.revealActiveFileInWindows"
  },
  {
    "key": "cmd+k cmd+r",
    "command": "-git.revertSelectedRanges",
    "when": "isInDiffEditor"
  },
  {
    "key": "cmd+k cmd+r",
    "command": "-workbench.action.keybindingsReference"
  },
  {
    "key": "cmd+k shift+alt+cmd+c",
    "command": "-copyRelativeFilePath",
    "when": "editorFocus"
  },
  {
    "key": "cmd+1",
    "command": "-workbench.action.focusFirstEditorGroup"
  },
  {
    "key": "cmd+2",
    "command": "-workbench.action.focusSecondEditorGroup"
  },
  {
    "key": "cmd+3",
    "command": "-workbench.action.focusThirdEditorGroup"
  },
  {
    "key": "cmd+4",
    "command": "-workbench.action.focusFourthEditorGroup"
  },
  {
    "key": "cmd+5",
    "command": "-workbench.action.focusFifthEditorGroup"
  },
  {
    "key": "cmd+6",
    "command": "-workbench.action.focusSixthEditorGroup"
  },
  {
    "key": "cmd+c",
    "command": "-gitlens.views.branches.copy",
    "when": "gitlens:enabled && focusedView =~ /^gitlens\\.views\\.branches/"
  },
  {
    "key": "cmd+c",
    "command": "-gitlens.views.commits.copy",
    "when": "gitlens:enabled && focusedView =~ /^gitlens\\.views\\.commits/"
  },
  {
    "key": "cmd+c",
    "command": "-gitlens.views.contributors.copy",
    "when": "gitlens:enabled && focusedView =~ /^gitlens\\.views\\.contributors/"
  },
  {
    "key": "cmd+c",
    "command": "-gitlens.views.fileHistory.copy",
    "when": "gitlens:enabled && focusedView =~ /^gitlens\\.views\\.fileHistory/"
  },
  {
    "key": "cmd+c",
    "command": "-gitlens.views.lineHistory.copy",
    "when": "gitlens:enabled && focusedView =~ /^gitlens\\.views\\.lineHistory/"
  },
  {
    "key": "cmd+c",
    "command": "-gitlens.views.remotes.copy",
    "when": "gitlens:enabled && focusedView =~ /^gitlens\\.views\\.remotes/"
  },
  {
    "key": "cmd+c",
    "command": "-gitlens.views.repositories.copy",
    "when": "gitlens:enabled && focusedView =~ /^gitlens\\.views\\.repositories/"
  },
  {
    "key": "cmd+c",
    "command": "-gitlens.views.searchAndCompare.copy",
    "when": "gitlens:enabled && focusedView =~ /^gitlens\\.views\\.searchAndCompare\\b/"
  },
  {
    "key": "cmd+c",
    "command": "-gitlens.views.stashes.copy",
    "when": "gitlens:enabled && focusedView =~ /^gitlens\\.views\\.stashes/"
  },
  {
    "key": "cmd+c",
    "command": "-gitlens.views.tags.copy",
    "when": "gitlens:enabled && focusedView =~ /^gitlens\\.views\\.tags/"
  },
  {
    "key": "cmd+c",
    "command": "-gitlens.views.worktrees.copy",
    "when": "gitlens:enabled && focusedView =~ /^gitlens\\.views\\.worktrees/"
  },
]
```
