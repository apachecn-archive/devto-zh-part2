# 你能帮我在 vs 代码中设置 eslint 吗

> 原文：<https://dev.to/itsjzt/can-you-help-me-to-set-up-eslint-in-vs-code--1j2i>

我已经试过很多次了，我想问别人会更好。

错误消息:

```
Cannot read config file: C:\Users\itsjzt\.eslintrc.json Error: Unexpected token � in JSON at position 0 
```

Enter fullscreen mode Exit fullscreen mode

设置:

```
{
  "editor.tabSize": 2,
  "files.eol": "\n",
  "files.trimTrailingWhitespace": true,
  "workbench.colorTheme": "Dracula",
  "editor.minimap.enabled": false,
  "workbench.iconTheme": "material-icon-theme",
  "terminal.integrated.fontSize": 16,
  "eslint.packageManager": "yarn",
  "eslint.options": { "configFile": "C:\\Users\\itsjzt\\.eslintrc.json" }
} 
```

Enter fullscreen mode Exit fullscreen mode

插件:

```
dbaeumer.vscode-eslint
dracula-theme.theme-dracula
lukehoban.Go
PKief.material-icon-theme
WakaTime.vscode-wakatime
WallabyJs.quokka-vscode 
```

Enter fullscreen mode Exit fullscreen mode

. eslintrc.json

```
{
  "extends": "airbnb"
} 
```

Enter fullscreen mode Exit fullscreen mode

操作系统:`Windows 10 1709`