# 显示/隐藏带有普通 JS 的滚动元素

> 原文：<https://dev.to/changoman/showhide-element-on-scroll-w-vanilla-js-3odm>

这里有一个简单的 JavaScript 脚本，它会根据你在页面上的上下滚动来添加/删除一个类。过去，我一直使用一个名为 [Headroom.js](https://wicky.nillia.ms/headroom.js/) 的小工具，但我想看看我是否能从头开始复制基本功能。

下面是结果:
[自定义净空演示](http://custom-headroom.surge.sh/) ( [查看来源](https://github.com/codebushi/custom-headroom))

看一看[index.html](https://github.com/codebushi/custom-headroom/blob/master/index.html)的源代码。

运行的主脚本是这个块:

```
let scrollPos = 0;
const nav = document.querySelector('.site-nav');

function checkPosition() {
  let windowY = window.scrollY;
  if (windowY < scrollPos) {
    // Scrolling UP
    nav.classList.add('is-visible');
    nav.classList.remove('is-hidden');
  } else {
    // Scrolling DOWN
    nav.classList.add('is-hidden');
    nav.classList.remove('is-visible');
  }
  scrollPos = windowY;
}

window.addEventListener('scroll', checkPosition); 
```

Enter fullscreen mode Exit fullscreen mode

我们向`window`添加了一个`scroll`事件监听器，并在用户每次滚动页面时触发`checkPosition`函数。

查看`function checkPosition(){}`,我们首先得到`window.scrollY`位置，这是我们距离页面顶部的像素距离。然后我们将它与每次滚动后更新到新的`window.scrollY`位置的`scrollPos`变量进行比较。

如果`windowY`小于前一个`scrollPos`，那么我们知道我们正在向上滚动，我们可以相应地给我们的`nav`元素添加一个类。在这种情况下，我们希望菜单向上滚动时显示，向下滚动时隐藏。

基本上就是这样，但是我们还想增加一些限制，这样我们就不会在长时间的滚动会话中疯狂地触发事件。为此，我们添加了一个`debounce`函数，确保它在启动前等待 10 毫秒。

我们可以这样替换我们的`window`事件监听器:

```
function debounce(func, wait = 10, immediate = true) {
  let timeout;
  return function() {
    let context = this, args = arguments;
    let later = function() {
      timeout = null;
      if (!immediate) func.apply(context, args);
    };
    let callNow = immediate && !timeout;
    clearTimeout(timeout);
    timeout = setTimeout(later, wait);
    if (callNow) func.apply(context, args);
  };
};

window.addEventListener('scroll', debounce(checkPosition)); 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 需要做的只是向我们的元素添加或移除一个类，剩下的事情由我们的 [CSS](https://github.com/codebushi/custom-headroom/blob/master/style.css) 来处理。

我知道这没有 Headroom.js 灵活，但它是一个精简的版本，可能是滚动事件的一个很好的介绍。

查看[https://codebushi.com/](https://codebushi.com/)获取更多我的网络开发技巧和资源。