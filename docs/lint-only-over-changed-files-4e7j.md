# 仅覆盖已更改的文件

> 原文：<https://dev.to/ohbarye/lint-only-over-changed-files-4e7j>

你的项目越大，花费的时间就越长。

因此，下面的任务非常有用，因为我们基本上只想对更改过的文件运行 lint。

```
#  package.json  {  "scripts":  {  "tslint":  "git diff --name-only develop... | grep -E '\\.tsx?$' | xargs yarn tslint"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我这里指的是`tslint`，但同样适用于`eslint`、`stylelint`等。