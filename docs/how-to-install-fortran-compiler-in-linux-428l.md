# 如何在 Linux 下安装 Fortran 编译器

> 原文：<https://dev.to/xeroxism/how-to-install-fortran-compiler-in-linux-428l>

[![gnufortran image banner](img/11113240241103502957b3e38135b4c9.png)T2】](https://i0.wp.com/fossnaija.com/wp-content/uploads/2018/10/gnufortran-image-banner.png?ssl=1)

在这篇文章中，我将向你展示如何用一些简单的步骤在 ubuntu [Linux](https://fossnaija.com/linux-installation-dual-booting-with-windows-7/) 中建立一个 Fortran 开发环境。

## GFORTRAN

[![gfortran man page](img/84ae831c808ed6a595b3f6c96289606b.png)T2】](https://i2.wp.com/fossnaija.com/wp-content/uploads/2018/10/gfortran-man-page.png?ssl=1)

尽管有许多不同的 Fortran 编译器可用于 Linux 系统；我将安装 gfortran(或 GNUfortran)，来自 [**GNU**](https://www.gnu.org/software//gcc/fortran/) 的 Fortran 编译器，(许多杰出和重要的自由软件的保管人)。之所以选择它，是因为它易于安装，并且得到了自由和开源软件社区中一个著名组织(GNU)的大力支持。

在你的 Linux 机器上打开你的[终端](https://fossnaija.com/the-anatomy-of-the-linux-command-line/)(Ubuntu systems =>**CTRL+Alt+T**的快捷键)，输入下面的命令，首先更新你的系统软件库:

```
sudo apt-get update 
```

然后安装 gfortran:

```
sudo apt-get install gfortran 
```

全部完成！！！

快乐的 Linux！

帖子[如何在 Linux 上安装 Fortran 编译器](https://fossnaija.com/how-to-install-fortran-compiler-in-linux/)最早出现在 [Foss Naija](https://fossnaija.com) 上。