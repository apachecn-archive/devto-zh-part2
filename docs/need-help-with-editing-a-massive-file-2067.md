# 需要帮助编辑一个大文件

> 原文：<https://dev.to/hoahchris/need-help-with-editing-a-massive-file-2067>

我有一个非常大的网站地图 XML 文件，我需要改变。我需要删除所有不包含对“”的引用的节点。jpg”。

这里有一个例子:

```
 <url>
    <loc>https://www.mywebsite.com/page-without-image</loc>
    <lastmod>2018-06-05</lastmod>
    <changefreq>daily</changefreq>
    <priority>0.7</priority>
      <image:image>
  </url>
  <url>
    <loc>https://www.mywebsite.com/page-with-image</loc>
    <lastmod>2018-06-05</lastmod>
    <changefreq>daily</changefreq>
    <priority>0.9</priority>
      <image:image>
        <image:loc>https://www.mywebsite.cimg/image-on-this-page.jpg</image:loc>
        <image:title>image title</image:title>
      </image:image>
  </url> 
```

Enter fullscreen mode Exit fullscreen mode

我需要使用 SublimeText 或任何有能力的程序检查整个文件，并删除所有没有。jpg 在里面。

有什么建议吗？

谢谢！