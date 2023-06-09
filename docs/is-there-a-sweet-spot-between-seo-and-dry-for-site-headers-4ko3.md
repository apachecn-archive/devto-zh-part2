# 对于站点标题，SEO 和 DRY 之间有没有一个最佳平衡点？

> 原文：<https://dev.to/jessachandler/is-there-a-sweet-spot-between-seo-and-dry-for-site-headers-4ko3>

所以，我有一个问题要问你。这里有一个假设的情况:

## 情况

你做到了，你建立了一个网站。聪明的是，你有一个带页眉和页脚的文件夹，你只需要把它放在每一页上。但是，现在每个页面都有相同的 meta 标签。这使得搜索引擎算法将你的网站推入无聊重复内容的土地，这可不好。

## 解？<——这就是我要放你思想的地方！！！

**注意:我会用你们的精彩回答来更新这个解决方案部分！**

### 哲基尔

我已经在 jekyll 中为静态站点解决了这个问题，方法是在构建过程中在直接从 YAML 提取的头中包含液体标签。这是顺利和容易的。

在/includes 文件夹中，我有一个名为“head.html”的文件，它的结构是这样的:

```
<head>
  <!-- other head meta stuffs like width and utf-->

  {{ site.name }}{% if page.title and page.url != "/" %} | {{ page.title }}{% endif %}
  <meta name="description" content="{% if page.description %} | {{ page.description }}{% else %}{{ site.description }}{% endif %}">

   <!-- other head stuffs like twitter and css -->
</head> 
```

这个“head.html”包含在/layouts 文件夹的大部分页面布局中，就像这样(这是“default . html”:

```
<!DOCTYPE html>
<html>
  {% include head.html %}
  <body>
    {% include header.html %}

    <div class="page-content">
      <div class="wrapper">
        {{ content }}
      </div>
    </div>
    {% include footer.html %}
  </body>
</html> 
```

然后，在书页中，我可以用 YAML，像这样:

```
---
layout: default
title: space invaders in the wild
permalink: /spaceinvaders/
description: a gallery of space invaders that I have seen
img: somedir/genericspaceinvader.png
---
## My super duper header
My super duper content 
```

现在，这个页面会显示出来(对搜索引擎...以及最终其他人类)与元像:

```
 ME: space invaders in the wild
  <meta name="description" content="a gallery of space invaders that I have seen"> 
```

也许不是最好的搜索引擎优化，但不一样的所有 20，000 其他网页显示为:“我”与描述“我的描述”或什么。

### 其他站点是怎么做的？有没有类似的做法？