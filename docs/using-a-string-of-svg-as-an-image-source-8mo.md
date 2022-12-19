# 使用一串 SVG 文本作为图像源

> 原文：<https://dev.to/benjaminblack/using-a-string-of-svg-as-an-image-source-8mo>

我的帖子通常是我自己的笔记和参考资料，我在这里发布，希望其他人会觉得有用。

TL；博士:

```
let svg = ` URL.revokeObjectURL(url), {once: true}); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

以编程方式将 SVG 组成一个字符串有时很有用。使用该字符串作为图像的`src`相当简单(有一个“gotcha”)。)

第一步:你需要一串 SVG 文本。

```
let svg = `Enter fullscreen mode Exit fullscreen mode

第二步:将字符串转换成 blob。

```
let blob = new Blob([svg], {type: 'image/svg+xml'}); 
```

Enter fullscreen mode Exit fullscreen mode

第三步:创建 blob 的 URL。

```
let url = URL.createObjectURL(blob); 
```

Enter fullscreen mode Exit fullscreen mode

第四步:创建一个引用 URL 的图像。

```
let image = document.createElement('img');
image.src = url; 
```

Enter fullscreen mode Exit fullscreen mode

第五步:一旦图像加载后，撤销 URL 进行清理。

```
image.addEventListener('load', () => URL.revokeObjectURL(url), {once: true}); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

但是，下面的 ***不起作用*** :

```
let svg = `

    <circle cx="25" cy="25" r="20"/>
`; 
```

Enter fullscreen mode Exit fullscreen mode

这会导致图像损坏:

[![broken image](../Images/23d0fe410946eb8cc7d6f4502e014d4c.png "No xmlns")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g9OqsXbr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/3Lsgp7A.png)

属性*的`xmlns`必须*包含在``元素中:

```
let svg = `

   <circle cx="25" cy="25" r="20"/>
`; 
```

Enter fullscreen mode Exit fullscreen mode

现在成功了:

[![working image](../Images/c2b1309c406b217f9e6ae14d706c076f.png "Yes xmlns")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qvvzdZ9u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/G6bw388.png)

在这种情况下，Chrome 不能容忍缺少`xmlns`属性，我想这是一致的:只有内嵌在 HTML 标记中的``、[才允许省略`xmlns`属性](https://www.w3.org/TR/html5/syntax.html#foreign-elements)。用作`<img>`的`src`的外部 SVG 必须包含它，即使 SVG 是“内联的”,因为它是由文档本身作为 blob 创建的，即使 MIME 类型是`image/svg+xml`。