# 你如何构建你的应用程序？

> 原文：<https://dev.to/hdennen/how-do-you-structure-your-app-1jed>

我们有一个相当大的应用程序，目前在高层次上分为视图、逻辑、模型、通信和实用程序。所有文件都有别名，我们有脚本来处理任何移动。在很大程度上，我们按照目的进行分组，但有时寻找东西仍然是一种负担。

这是我们团队第二次尝试重组，所以我很想听听大家的经验。

### 举例:

```
|-view
  |-engine
    |-core
    |-animation

  |-feature
    |-featureName 
```