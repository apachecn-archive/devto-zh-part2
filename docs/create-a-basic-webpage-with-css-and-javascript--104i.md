# 用 CSS 和 JavaScript 创建一个基本的网页

> 原文：<https://dev.to/programliftoff/create-a-basic-webpage-with-css-and-javascript--104i>

*本文最初发表在[programmingliftoff.com](http://programmingliftoff.com/)上，名为[用 CSS 和 JavaScript 创建一个基本的网页](http://programmingliftoff.com/create-basic-webpage-css-javascript/)。*

[![Screenshot of finished webpage](../Images/d0ade52f22a24e1aa08bd35e57d3732c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YxX7s4kI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7lmon5ilpfg8l8c7coyl.png)

*注意:如果你已经有了自己的网站，只需要上传到 GitHub 并启用 GitHub Pages，请阅读[上传网站到 GitHub Pages](http://programmingliftoff.com/upload-website-github-pages/)T3】*

宁愿看这个教程也不看？[点击这里在 YouTube 上观看](https://youtu.be/CFkQk_6xdS8)。

厌倦了创建没有风格的无聊 HTML 页面？然后是时候添加一些 CSS 和 JavaScript 来活跃你的网页了！在本教程中，我们将用一个基本的 index.html 文件创建一个网站，并添加一些 CSS 和 JavaScript 文件，使其更加精彩。然后我们会把网页上传到 GitHub 作为 GitHub Pages 网站！请注意，本教程中展示的在网页中添加 CSS 和 JavaScript 文件的方法并不特定于 GitHub 页面。这些方法将适用于任何网站和任何主机(GitHub、GitLab、BitBucket 等)...).事不宜迟，我们开始吧！

首先打开您最喜欢的文本编辑器并创建一个新文件夹(您可以随意命名该文件夹)。在该文件夹中，创建一个名为 index.html 的文件。

将以下代码添加到 index.html:

```
<!DOCTYPE html>
<html>
  <head>
    My webpage!
  </head>
  <body>
    <h1>Hello, World!</h1>
    <h4 id='date'></h4>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

如果你在浏览器中打开网页，你应该只看到“你好，世界！”印在屏幕上。

#### 向我们的网页添加 JavaScript

接下来我们将学习如何将 JavaScript 代码从一个单独的文件导入到“index.html”中。创建一个名为“javascript”的文件夹。在该文件夹中，创建一个名为“index.js”的文件。在这个 JavaScript 文件中，添加下面一行:

```
document.getElementById('date').innerHTML = new Date().toDateString(); 
```

Enter fullscreen mode Exit fullscreen mode

然后，在“index.html”的`<head>`标记中，添加以下代码行:

```
<script async src="./javascript/index.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

现在保存文件并刷新浏览器。你应该看到当前日期印在“你好，世界！”。那么这是怎么回事呢？再看一下我们添加的 JavaScript。如您所见，它获取 id 为“date”的元素(这是我们网页中的

#### 标签)，并将其中的文本设置为一个新的 Date()对象，该对象被转换为可读性更好的字符串。

然后，我们在“index.html”页面中添加了一个

#### 向我们的网页添加 CSS

首先创建一个名为“styles”的文件夹。在该文件夹中，创建一个名为“styles.css”的文件。将以下代码键入或粘贴到“styles.css”中:

```
body {
  text-align: center;
  background-color: #f0e8c5;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在回到“index.html”文件，在标签中添加下面的<link>标签(就在我们在上一节中添加的

```
<link rel="stylesheet" href="styles/styles.css" /> 
```

Enter fullscreen mode Exit fullscreen mode

现在刷新浏览器中的“index.html”页面。太棒了。我们已成功将样式添加到网页中！查看我们添加到“styles.css”中的代码，您可以看到我们将文本居中，并为网页主体添加了浅棕色背景色。然后我们添加了一个属性为`rel="stylesheet"`的<link>标签到‘index . html’。这告诉浏览器我们将从一个单独的文件中加载 CSS 样式。我们还添加了属性`href="styles/styles.css"`。这告诉浏览器，我们要添加的样式位于“styles.css”文件中，该文件位于“styles”文件夹中。厉害！现在我们已经在网页中添加了 JavaScript 和 CSS，让我们做得更好，添加一些带有甜美风格的图片！

#### 添加一些甜美的 CSS 样式！

用下面的代码替换“index.html”页面的内容:

```
<!DOCTYPE html>
<html>
  <head>
    My webpage!
    <link rel="stylesheet" href="styles/styles.css" />
    <script async src="./javascript/index.js"></script>
  </head>
  <body>
    <h1>Hello, World!</h1>
    <h4 id='date'></h4>

    <div class="image-section">
      <div class="section-style">
        <img src="https://source.unsplash.com/random/400x200" alt="" />
        <p>A random image courtesy of unsplash.com.</p>
      </div>

      <div class="section-style">
        <img src="https://source.unsplash.com/random/400x200" alt="" />
        <p>A random image courtesy of unsplash.com.</p>
      </div>
    </div>

    <div class="image-section">
      <div class="section-style">
        <img src="https://source.unsplash.com/random/400x200" alt="" />
        <p>A random image courtesy of unsplash.com.</p>
      </div>

      <div class="section-style">
        <img src="https://source.unsplash.com/random/400x200" alt="" />
        <p>A random image courtesy of unsplash.com.</p>
      </div>
    </div>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

也用下面的代码替换“styles.css”的内容:

```
body {
  text-align: center;
  background-color: #f0e8c5;
}

div {
  margin-top: 15px;
}

.image-section {
  display: flex;
  justify-content: center;
}

.section-style {
  margin-right: 25px;
  margin-left: 25px;
  background-color: white;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在刷新你的网页。不错！我们有一个可爱的网页，上面有来自 unsplash.com 的图片！每次刷新页面，你都会看到一个新的随机图像。很酷，对吧？！

[![Image of basic webpage with four pictures from unsplash](../Images/3e2e83f6550aaffd9721d493ca09b582.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UZiZrozk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://programmingliftoff.com/wp-content/uploads/2018/02/create-website-css-and-js.png)

如果你有任何问题，请在 YouTube 上观看视频教程。

### 后续步骤:将网站上传到 GitHub 页面

好了，现在我们已经在本地创建了我们的网站，让我们使用 GitHub 页面将它免费发布到互联网上。前往[将网站上传到 GitHub Pages](http://programmingliftoff.com/upload-website-github-pages/) 了解如何将您的网站上传到 GitHub Pages。

感谢阅读！
-安德鲁@编程升空
**网址:**【programmingliftoff.com】T4