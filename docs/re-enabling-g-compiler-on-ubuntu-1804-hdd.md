# 在 Ubuntu 18.04 上重新启用 g++编译器(已修复)

> 原文：<https://dev.to/theredspy15/re-enabling-g-compiler-on-ubuntu-1804-hdd>

所以我用一个脚本禁用了 Ubuntu 18.04 上的 g++编译器来保护系统。但是我需要重新启用编译器来编译一些东西

然而，无论我在哪里看，我只看到如何安装 g++的教程，其他的什么都没有。

脚本中禁用编译器的部分:

`chmod 000 /usr/bin/*g++ >/dev/null 2>&1`