# c9x 编辑

> 原文：<https://dev.to/btbytes/c9x-edit--i8n>

为了在 Mac 上编译 [c9x edit](http://c9x.me/edit/) ，你必须做以下事情:

```
export PKG_CONFIG_PATH=/opt/X11/lib/pkgconfig
make
./obj/edit 
```

Enter fullscreen mode Exit fullscreen mode

这将使用 X11 (XQuartz)启动`ed`窗口。