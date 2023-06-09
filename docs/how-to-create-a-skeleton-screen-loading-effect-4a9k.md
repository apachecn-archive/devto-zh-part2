# 如何创建骨架屏幕加载效果

> 原文：<https://dev.to/justalever/how-to-create-a-skeleton-screen-loading-effect-4a9k>

我的 Let's Build: With JavaScript 系列继续了新的一期，在这一期中，我将教您如何用普通的 JavaScript、HTML 和 CSS 创建一个框架屏幕加载效果。

这篇教程的灵感来自于我最近在我雇主的平台(Dribbble)上看到的一个趋势，以及许多我们都知道和喜欢的流行产品。例如，当你执行搜索时，Google images 就利用了这一特性，但它们更进一步，动态地确定要加载的图像的颜色。

通常被称为*骨架屏幕*效果的效果是 CSS 和 JavaScript 的结合，这种结合为页面上的组件产生可感知的加载状态。例如，内容卡可能有一个图像、标题和摘录的占位符，将在页面初始加载时显示。一旦内容被注入到 DOM 中，我们最终会看到用户最初想要的内容。

[查看密码本](https://codepen.io/webcrunchblog/pen/vVvPZM)

#### 为什么要用这种手法？

在我看来，传统的 CSS 微调器或图像加载器是更合适的解决方案，因为用户可以预测他们将要看到什么样的内容。将占位符与动画相结合来指示事情正在发生，这是告诉用户稍等片刻的一种非常好的方式。

[https://www.youtube.com/embed/-xU95CUTvHg](https://www.youtube.com/embed/-xU95CUTvHg)

### 这一切是怎么运作的？

对于本教程，我先说我在使用 JavaScript 内置的`setTimeout`函数伪造加载时间。有了这个基础，我可以继续说我们将使用一些基本的`HTML`来创建一个卡组件。我们的`SCSS`将允许我们创建卡片用户界面，并触发我们将显示的动画，直到我们想要的主要内容被渲染。

#### HTML 标记

我从一个容器 div 开始，它包装了我们的卡组件，并将其呈现在页面的中央。然后我创建一个新的`section`元素，它包含一个`figure`元素和一个`card-detail`元素。在`card-detail`元素中有一个`h3`和`p`标签。我们所有的标记都是空的，因为我将使用 JavaScript 注入内容。从 SEO 和可访问性的角度来看，有更好的方法来解决这个问题，但是我现在忽略这些(我知道，我不应该！).

注意在其中三个元素上添加了一个`loading`类。CSS 将针对这些，并创建一个动画背景图像/颜色场景。这种样式给人一种装载状态的感觉。

```
<div class="container">
  <section class="card">
    <figure class="card-image loading"></figure>
    <div class="card-detail">
      <h3 class="card-title loading"></h3>
      <p class="card-description loading"></p>
    </div>
  </section>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

#### CSS/SCSS

如果没有一些 CSS，卡片看起来就不像卡片，所以下面是我用来获得外观和感觉的样式。注意`.loading`类。这就是骨架屏幕效果的神奇之处。我们使用一个独特的设置渐变。从那里我们无限地激活它。

```
$border-color: #dae1e7;
$background-color: #f4f4f4;
$text-color: #333;

body {
  background: $background-color;
  margin: 0;
  padding: 0;
  font-family: system-ui,-apple-system,BlinkMacSystemFont,Segoe UI,Roboto,Oxygen,Ubuntu,Cantarell,Fira Sans,Droid Sans,Helvetica Neue;
  color: $text-color;
}

.container {
  margin: 2rem auto;
  max-width: 800px;
  display: flex;
  justify-content: center;
}

.card {
  overflow: hidden;
  background: white;
  border-radius: .25rem;
  max-width: 380px;
  width: 380px;
  box-shadow: 
    0 15px 30px 0 rgba(0,0,0,0.05),
    0 5px 15px 0 rgba(0,0,0,.05);
  transition: ease box-shadow 0.3s;
  &:hover {
   box-shadow: 
    0 15px 60px 0 rgba(0,0,0,0.08),
    0 5px 25px 0 rgba(0,0,0,.08);
  }
}

.card-detail {
  padding: .5rem 1rem;

  h3 { 
    font-size: 1.5rem; 
    margin-bottom: none; 
    line-height: .09;
  }

  p {
    line-height: 1.3rem;  
  }
}

.card-image {
  margin: 0;
  padding: 0;
  height: 200px;
  overflow: hidden;

  img {
    max-width: 100%;
    height: auto;
  }
}

.loading {
  position: relative;
  background-color: #E2E2E2;

  &.card-image {
    border-radius: 0;
  }

  &::after {
    display: block;
    content: '';
    position: absolute;
    width: 100%;
    height: 100%;
    transform: translateX(-100%);
    background: linear-gradient(90deg, transparent, rgba(255, 255, 255, .2), transparent);
    animation: loading 1.5s infinite;
  }
}

@keyframes loading {
  100% {
    transform: translateX(100%);
  }
}

.card-title.loading {
  height: 1.8rem;
}

.card-image.image {
  max-width: 100%;
  height: auto;
}

.card-description.loading {
  height: 80px;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### JavaScript

这里的 JavaScript 相当简单。我们构建了一个名为`renderCard`的函数来一次触发所有东西。在这个函数中，我们设置想要应用骨架效果的元素的内容。除此之外，我还创建了一个新的函数，这里引用了它。

另一个函数挂钩到 JavaScript API，在我们的`HTML`上的`figure`元素内动态创建一个新的`img`元素。在那里，我添加了一些属性，让图像以我们想要的方式显示。

```
const cardImage = document.querySelector('.card-image');
const cardTitle = document.querySelector('.card-title');
const cardDesc = document.querySelector('.card-description');

const renderCard = () => {
  cardTitle.textContent = 'Card Title Yo!';  
  cardDesc.textContent = 'Lorem ipsum dolor, sit amet consectetur adipisicing elit. Vero dicta repellat quibusdam assumenda at distinctio similique eos? Officiis voluptatem accusamus, id odit, quos eaque nemo, dicta quidem eum eligendi veritatis.';
  createImage();
  cardTitle.classList.remove('loading');
  cardDesc.classList.remove('loading');
  cardImage.classList.remove('loading');
};

function createImage() {
  const img = new Image();
  img.classList.add("image");
  img.setAttribute('alt', 'A cloud day at a mountain base');
  img.setAttribute('src', 'https://images.unsplash.com/photo-1516646255117-f9f933680173?ixlib=rb-0.3.5&q=85&fm=jpg&crop=entropy&cs=srgb&ixid=eyJhcHBfaWQiOjE0NTg5fQ&s=dc874984018a4253ba7d2a3c53387135');
  cardImage.appendChild(img);
}

setTimeout(() => {
  renderCard();
}, 5000);

// window.addEventListener('load', () => {
// renderCard();
// }); 
```

Enter fullscreen mode Exit fullscreen mode

### 何去何从？

骨架屏幕加载技术是一个伟大的技术。这个例子相当简单，你可以从数据库、某个`JSON`或其他场景中获取内容，而不是用 JavaScript 设置内容。所有这些都与 Ajax、fetch API 或类似的获取数据的东西有关(至少只使用 JavaScript)。我计划在接下来的视频中更深入地探讨这些概念，所以请继续关注。

#### 到目前为止的级数

*   [让我们构建:使用 JavaScript–DIY 下拉菜单和响应菜单](https://web-crunch.com/lets-build-with-javascript-diy-dropdowns-responsive-menus/)
*   [让我们用 JavaScript 构建——带 Cookies 的广播栏](https://web-crunch.com/lets-build-with-javascript-broadcast-bar-cookies/)
*   [让我们用 JavaScript 构建:粘性导航](https://web-crunch.com/lets-build-with-javascript-sticky-nav/)
*   [让我们构建:用 JavaScript 动态标签](https://web-crunch.com/how-to-create-tabs-with-vanilla-javascript)
*   [让我们构建:用 JavaScript–Modals](https://web-crunch.com/lets-build-with-javascript-how-to-code-modal-vanilla-javascript/)
*   [让我们构建:使用 JavaScript–html 5 视频播放器](https://web-crunch.com/lets-build-with-javascript-html5-video-player/)
*   [让我们构建:用 JavaScript——手风琴](https://web-crunch.com/how-to-create-accordion-vanilla-javascript/)

帖子[如何创建骨架屏幕加载效果](https://web-crunch.com/how-to-create-skeleton-screen-loading-effect/)最早出现在[网上](https://web-crunch.com)。