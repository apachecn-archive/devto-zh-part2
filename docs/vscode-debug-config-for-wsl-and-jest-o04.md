# wsl 和 jest 的 vscode 调试配置

> 原文：<https://dev.to/marzelin/vscode-debug-config-for-wsl-and-jest-o04>

当使用 jest [文档](https://github.com/Microsoft/vscode-recipes/tree/master/debugging-jest-tests)中的配置时，执行不会在指定的断点处停止，但是以下配置适用于 wsl:

```
{  "version":  "0.2.0",  "configurations":  [  {  "type":  "node",  "request":  "launch",  "name":  "Jest All",  "program":  "${workspaceFolder}/node_modules/jest/bin/jest",  "args":  ["--runInBand"],  "useWSL":  true  },  ]  } 
```

Enter fullscreen mode Exit fullscreen mode