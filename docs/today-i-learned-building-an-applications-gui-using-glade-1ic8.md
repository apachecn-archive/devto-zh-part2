# 今天我学习了:使用 Glade 构建应用程序的 GUI

> 原文：<https://dev.to/jsalvador/today-i-learned-building-an-applications-gui-using-glade-1ic8>

我已经为许多任务写了很多 Python 脚本，比如网络抓取或电报酷的东西。但直到本周，我从未设计过非基于网络的用户界面。我开始用 Glade 和 Gtk。

只需将一些小部件拖放到一个`GtkApplicationWindow`中，将其保存到项目的文件夹中并加载即可。

```
 import gi
    gi.require_version('Gtk', '3.0')

    from gi.repository import Gtk

    class ButtonHander():
        # button action's code here! 
    builder = Gtk.Builder()
    builder.add_from_file("gui.glade")
    builder.connect_signals(ButtonHandler())

    window = builder.get_object("mainWindow")
    window.show_all()

    Gtk.main() 
```

Enter fullscreen mode Exit fullscreen mode

因为图形用户界面很酷！