# 启动闪亮的应用程序，而不阻止会话

> 原文：<https://dev.to/martinmodrak/launch-shiny-app-without-blocking-the-session-3c1p>

这是我在泰勒·摩根-沃尔的推特上发现的一个巧妙的技巧，最初是由 T2·郑中发明的。你可以运行任何闪亮的应用程序，而不会阻塞会话。我运行 ShinyStan 不阻塞的助手函数如下:

```
launch_shinystan_nonblocking <- function(fit) {
  library(future)
  plan(multisession)
  future(
    launch_shinystan(fit) #You can replace this with any other Shiny app
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

希望有所帮助！