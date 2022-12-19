# 从普通 reST 生成 Reveal.js 表示的 Sphinx 扩展

> 原文：<https://dev.to/attakei/sphinx-extension-to-generate-revealjs-presentation-from-plain-rest-58ip>

我创建 Sphinx 扩展来将 Reveal.js 表示从 **plain** reStructuredText 转换。如果你想用 Sphinx(reStructuredText)编写 Reveal.js，请尝试一下

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [阿塔凯](https://github.com/attakei) / [狮身人面像-复仇女神](https://github.com/attakei/sphinx-revealjs)

### sphinx builder 展示演示文稿

<article class="markdown-body entry-content container-lg" itemprop="text">

# 狮身人面像

[![](img/9cd3d68ac70b4f9c5187e6b39f69fdc6.png) ](https://pypi.org/project/sphinx-revealjs/) [![](img/042838fade83795eaf81bbc376347315.png) ](https://github.com/attakei/sphinx-revealjs/actions) [![https://travis-ci.org/attakei/sphinx-revealjs.svg?branch=master](img/56e54cc2d0d91b5ef59949c18a640151.png)](https://travis-ci.org/attakei/sphinx-revealjs)

使用主题生成 Reveal.js 演示的 Sphinx 扩展

## 概观

该扩展从**标准** reStructuredText 生成 Reveal.js 演示文稿。

它包括这些特征。

*   从 reST 转换为 reveal.js 样式 HTML 的自定义生成器
*   要启用模板以独立于本地呈现演示

## 装置

```
$ pip install sphinx-revealjs
```

## 使用

1.  创建您的 sphinx 文档

2.  编辑 conf.py 以使用此扩展名

    > T2
    > 
    > ```
    > extensions = [
    >     'sphinx_revealjs',
    > ]
    > ```

3.  标准文档样式的编写源

4.  将源代码构建为 Reveal.js 演示文稿

    > T2
    > 
    > ```
    > $ make revealjs
    > ```

## 更改日志

参见 [it](https://raw.githubusercontent.com/attakei/sphinx-revealjs/master/README.rst/./CHANGES.rst)

## 期货

*   作为无演示文稿的索引模板
*   CDN 支持

## 版权

Apache-2.0 许可证。请看[执照](https://raw.githubusercontent.com/attakei/sphinx-revealjs/master/README.rst/./LICENSE)。

</article>

[View on GitHub](https://github.com/attakei/sphinx-revealjs)

如果您想查看源代码和输出，请单击这些链接。这是一对简单 reST 源代码和生成的演示。

*   来源
    *   [https://raw . githubusercontent . com/Atta kei/sphinx-reveal js/master/demo/index . rst](https://raw.githubusercontent.com/attakei/sphinx-revealjs/master/demo/index.rst)
*   输出到
    *   [https://attakei.github.io/sphinx-revealjs/index.html](https://attakei.github.io/sphinx-revealjs/index.html)

# 安装和使用

## 安装

```
$ pip install git+https://github.com/attakei/sphinx-revealjs 
```

## 用法

1.  给你的狮身人面像添加扩展名`conf.py`

```
extensions = [
    'sphinx_revealjs',
] 
```

1.  通过 reST 编写您的演示源，并构建

```
$ make revealjs 
```

# 概念

*   尽可能多地使用默认节点和指令
*   在一个 Sphinx 文档中管理多个表示
    *   并且，可以根据文档切换主题

## 在休息时支持功能

*   节(convert reveal.js 节)
*   注释(用于演讲者注释)
*   代码块(convert reveal.js 样式代码块)
*   和其他指令(继承 Sphinx HTML5 builder)

# 转换的简短例子

你的写作来源是这样的..

```
=====
Title
=====

Section 1
=========

content 1

Section 2
=========

content 2 
```

它可以转换这个

```
<section>
  <h1>Title</h1>
</section>
<section>
  <h2>Section 1</h2>
  <div>content 1</div>
</section>
<section>
  <h2>Section 2</h2>
  <div>content 2</div>
</section> 
```

当然，支持嵌套节。

从

```
=====
Title
=====

Section 1
=========

content 1.1
-----------

content body

content 1.2
-----------

content body 
```

至

```
<section>
  <h1>Title</h1>
</section>
<section>
  <section>
    <h2>Section 1</h2>
  </section>
  <section>
    <h3>content 1.1</h3>
    <div>content body</div>
  </section>
  <section>
    <h3>content 1.2</h3>
    <div>content body</div>
  </section>
</section> 
```