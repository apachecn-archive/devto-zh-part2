# 悬停时弹出图像

> 原文：<https://dev.to/sarah_chima/image-popup-on-hover-1kee>

[![Image Popup on hover](../Images/be62090d27ac7a71c87974362c8eb076.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5RAzXUmw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dvj2hbywq/image/upload/q_49/v1539359213/ezgif.com-optimize_1_dtpz0u.gif)

最近，我的任务是在鼠标悬停时制作一个图像弹出窗口。上面这张 gif 描述了我的意思。我不知道如何去做，但在谷歌搜索了几分钟后，我发现了一种技术，它很容易实现，而且不使用 JavaScript。这种技术是将两个图像并排放置，一个是缩略图(较小的图像),另一个是当您将鼠标悬停在缩略图上时会出现的较大图像。我们开始吧

### 第一步:

我们将并排放置两幅图像，缩略图和大图，如下面的代码块所示。因此，在您的 HTML 文件中，添加以下代码，并记住正确链接您的图像文件。

```
<main>
  <ul>
    <li>
      <img src="img/thumbnail1.jpg" alt="adventure" >
      <span class="large">
          <img src="img/large1.jpg" class="large-image" alt="adventure" >
      </span>
    </li>
    <li>
      <img src="img/thumbnail2.jpg"" alt="cat" >
      <span class="large">
         <img src="img/large2.jpg" class="large-image" alt="cat" >
      </span>
    </li>
    <li>
        <img src="img/large3.jpg" alt="adventure" >
        <span class="large">
          <img src="img/large3.jpg" class="large-image" alt="adventure" >
        </span>
    </li>
  </ul>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

因为我们使用的是无序列表，所以我们将在 CSS 中设置`ul`和`li`的样式来移除列表样式。此外，我们使所有的列表项内联显示，图像显示样式。向链接到 HTML 代码的 CSS 文件中添加以下内容:

```
 ul {
      display: flex;
    }

    li {
      list-style-type: none;
      padding: 10px;
      position: relative;
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在，这是我们的页面应该看起来。

[![Image of work so far](../Images/abb16157931c89108059c489dd0290b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dwMUqKw4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dvj2hbywq/image/upload/v1538149115/Screen_Shot_2018-09-28_at_4.34.00_PM_xvpmna.png)

### 第二步

接下来，我们将使用绝对定位隐藏较大的图像。我们将使用我们添加到`span`中的类，较大的图像被放置在这个类中。

```
.large {
  position: absolute;
  left: -9999px;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，只剩下较小的图像。

[![Image of work so far](../Images/3cb9ee21e279b4a4ae52f91fed551858.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5uLsemTm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dvj2hbywq/image/upload/v1539359591/Screen-Shot-2018-10-12-at-4.48.17-PM-compressor_njes69.png) 
让我们添加一些样式，让大图在你悬停在小图上方时弹出。

```
li:hover .large {
  left: 20px;
  top: -150px;
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。当您将鼠标悬停在小图像上时，会出现大图像。

我们可以决定做更多的事情，给图片添加一个阴影，甚至给每张图片添加文字。我决定添加一个阴影，并给大图片一个边界半径。

```
.large-image {
  border-radius: 4px;
   box-shadow: 1px 1px 3px 3px rgba(127, 127, 127, 0.15);;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们结束了。你做过类似的事情吗？你是怎么做到的？我很想知道。