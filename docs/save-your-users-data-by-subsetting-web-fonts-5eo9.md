# 通过 web 字体子集化节省页面重量

> 原文：<https://dev.to/benjaminblack/save-your-users-data-by-subsetting-web-fonts-5eo9>

我的帖子通常是我自己的笔记和参考资料，我在这里发布，希望其他人会觉得有用。

网页字体对于设计来说是一个很好的东西，但是使用起来会很沉重，会降低用户体验。幸运的是，[所有现代浏览器都支持字体子集化](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/unicode-range)。

像 Google Web Fonts 这样的 CDN 可以很好地自动设置字体子集，提供优化的字体文件，但有时我们希望或需要托管自己的字体文件。

幸运的是，我们可以生产自己的子集字体。这种技术查看网站的文本，以构建该网站实际使用的字形(“字符”)列表，并构建排除所有未使用字形的子集字体文件，从而显著(有时[大幅](https://fonts.google.com/specimen/M+PLUS+1p))减小字体文件大小。

我们将利用 [fonttools](https://github.com/fonttools/fonttools) Python 包(及其依赖项)和 [glyphhanger](https://github.com/filamentgroup/glyphhanger) NPM 包。

作为一个例子，让我们来看看如果 Cracked 要自己托管字体文件，那么如何最小化 [Cracked](http://www.cracked.com/) 对 [Lato](https://fonts.google.com/specimen/Lato) 的使用。

但是首先，让我们安装一些必要的 Python 实用程序- `fonttools`、`zopfli`(对于 WOFF)和`brotli`(对于 WOFF2):

```
$ pip install fonttools zopfli brotli 
```

Enter fullscreen mode Exit fullscreen mode

Lato 的“常规”(正常)重量在 TTF 格式中为 117 KB，在 WOFF2 格式中为 32KB。

首先，我们需要积累我们网站实际使用的所有字形的列表。

使用`glyphhanger`实用程序输出一个 Unicode 范围，网站使用特定字体系列中的字形。选择这个工具是因为它的输出格式与`pyftsubset`实用程序的输入格式相同，并且由`unicode-range` CSS 属性使用。

```
$ npx glyphhanger http://cracked.com --family='Lato' > glyphs.txt
...

$ cat glyphs.txt
U+A,U+20,U+26-29,U+2C-39,U+3F,U+41-59,U+61-69,U+6B-70,U+72-7A,U+A9,U+2019 
```

Enter fullscreen mode Exit fullscreen mode

接下来，对于站点使用的字形子集，我们需要将这些字形从原始字体提取到子集字体中。

使用`fonttools`包附带的`pyftsubset`实用程序创建 WOFF/WOFF2 格式的子集:

```
$ pyftsubset Lato-Regular.ttf --unicodes-file=glyphs.txt --flavor=woff --with-zopfli

$ pyftsubset Lato-Regular.ttf --unicodes-file=glyphs.txt --flavor=woff2 
```

Enter fullscreen mode Exit fullscreen mode

这些命令产生`Lato-Regular.subset.woff`和`Lato-Regular.subset.woff2`。

当我运行这些命令时，得到的`Lato-Regular.subset.woff2`是 11 KB，大约是完整 WOFF2 文件大小的三分之一(不到 TTF 大小的 10%)。

这些子集字体文件可以像往常一样在一个`@font-face`规则中使用，增加一个`unicode-range`属性来指示字体文件中包含哪些字形。范围与`glyphhanger`实用程序输出到`glyphs.txt`的范围相同。

```
@font-face {
  font-family: 'Lato';
  font-style: normal;
  font-weight: normal;
  src: url('Lato-Regular.subset.woff2') format('woff2'),
    url('Lato-Regular.subset.woff') format('woff');
  unicode-range: U+A,U+20,U+26-29,U+2C-39,U+3F,U+41-59,U+61-69,U+6B-70,U+72-7A,U+A9,U+2019;
} 
```

Enter fullscreen mode Exit fullscreen mode

**ToDo** :将字体子集化集成到构建过程中。