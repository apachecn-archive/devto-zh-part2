# 将 TTF 字体转换为 WOFF2(和 WOFF)

> 原文：<https://dev.to/benjaminblack/converting-ttf-fonts-to-woff2-and-woff-3f5b>

我的帖子通常是我自己的笔记和参考资料，我在这里发布，希望其他人会觉得有用。

从 foundries 获得的字体，包括 Google web 字体，通常不包括 Web 字体格式 WOFF2 的字体文件，即使该字体 face 被特别许可在 Web 上使用；通常字体只在 TrueType(.ttf)格式。

幸运的是，将字体文件从 TTF 格式“转换”(真的，只是[编码和压缩](https://en.wikipedia.org/wiki/Web_Open_Font_Format#Specification))为 WOFF2 并不困难，不需要依赖粗略的在线服务。

Google 维护自由软件(MIT)命令行工具来压缩和解压缩 WOFF2 文件。存储库包含构建和运行实用程序的指令；但是为了冗余起见...

**打造:**

```
git clone --recursive https://github.com/google/woff2.git
cd woff2
make clean all 
```

Enter fullscreen mode Exit fullscreen mode

**跑:**

```
woff2_compress myfont.ttf 
```

Enter fullscreen mode Exit fullscreen mode

* * *

如果支持 IE 是必要的， [`ttf2woff`是在 NPM](https://github.com/fontello/ttf2woff) 可用的自由软件(MIT)遗留库，同样易于使用:

```
$ npx ttf2woff myfont.ttf myfont.woff 
```

Enter fullscreen mode Exit fullscreen mode