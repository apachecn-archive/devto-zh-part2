# 成绩反序列化！

> 原文：<https://dev.to/madsalama/achievement-deserialized-5d10>

终于征服了 C 反序列化！我发现一个函数正在将主循环变量的地址设置为函数中的局部变量的地址(这会弹出 duh——到处都是误导性的垃圾值),而不是在主循环中设置数据！你知道的越多！

PS:我正在开发一个 unix 域套接字守护进程，它处理和管理 inotify watch 请求，以自动化我工作中一个令人厌烦的部分！# c # unix # linux #初学者