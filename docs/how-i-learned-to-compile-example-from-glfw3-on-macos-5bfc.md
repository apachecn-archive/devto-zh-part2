# 我是如何学会在 macOS 上从 GLFW3 编译示例的

> 原文：<https://dev.to/shadoweaver/how-i-learned-to-compile-example-from-glfw3-on-macos-5bfc>

我刚开始学图形学。

这一切都是从哪里开始的:我们目前使用的 CG 教科书(Hearn，Baker，Carithers)使用 OpenGL 2.0(使用 GLUT 之类的东西)，而在我的系统上(MAC OS 10 . 12 . 6 @ rMBP 13“2013 年末”)，根据编译器警告，它只是被否决了。此外，我还读到现代 OpenGL 做事情的方式与众不同，而且非常漂亮，那么为什么不直接升级到 4.1 版呢(直到我的系统的最高支持)？(我甚至尝试过 Vulkan，但它对我的系统来说太新了。)

我知道我应该使用 [GLFW](http://www.glfw.org/) 与桌面窗口管理器交互，使用 [GLEW](https://glew.sourceforge.io/) 管理 OpenGL 扩展。在 macOS 上，它们很容易被[酿造](https://brew.sh/) ed:

```
$ brew install glfw --with-examples --with-test
$ brew install glew 
```

Enter fullscreen mode Exit fullscreen mode

但是当我试图用 GLFW 例子编译`simple.c`时，我迷失了。`linmath.h`很快被[找到了](https://github.com/datenwolf/linmath.h)，但是我该拿[高兴的](http://glad.dav1d.de/)怎么办？

显然我可以直接编译一些更简单的代码，就像在[这篇文章](http://seaneshbaugh.com/posts/creating-an-opengl-4-1-program-with-glew-and-glfw-in-xcode)中发现的那样，这篇文章没有像在`simple.c`中那样使用 GLAD。

我没有安装完整的 Xcode，用的是 brewed `gcc-7`。我花了一些时间才明白应该用

```
$ g++-7 -lglfw -lglew -framework OpenGL [filename] 
```

Enter fullscreen mode Exit fullscreen mode

它运行良好。

今天我偶然发现了 [learn OpenGL](https://learnopengl.com/Getting-started/Hello-Triangle) ，它展示了如何设置 GLAD。我应该下载它的某个版本，把头文件放到`/usr/include`里，还要编译和链接附带的 C 代码。按照这个指令，我终于编译好了自己的“simple.c”。