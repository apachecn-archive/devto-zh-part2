# 关闭和回调

> 原文：<https://dev.to/rpalo/closures-and-callbacks--30a9>

本周早些时候，我收到一封电子邮件，告诉我 Odin 项目刚刚升级了他们的 [JavaScript 课程](https://www.theodinproject.com/courses/javascript)。我查过了。如果你像我一样，因为所有的构建工具而推迟了学习 JavaScript 的时间，那么这个课程绝对适合你。我学习了 webpack 的基础知识(甚至提交了一个[拉请求](https://github.com/webpack/webpack.js.org/pull/1855)对 webpack 教程进行了修正！)，而且这一切都是以超级温和且初学者友好的速度进行的。在我学习这门课程的时候，在一次作业中，我发现了一个我认为很巧妙的模式。尤其是在几周前我在[发布了关于闭包](https://assertnotmagic.com/2018/02/10/closure-i-hardly-know-her/)的帖子之后，我知道我必须分享它。如果你在阅读我的代码示例时，对我的代码感到愤怒，请跳到[谵妄免责声明](#delirium-disclaimer)。

## 项目

目标是使用尽可能多的不同模块来测试 webpack 的捆绑能力。我应该[为一家餐馆](https://www.theodinproject.com/courses/javascript/lessons/restaurant-page)创建一个通用的登陆页面，它必须有一个基于标签的导航系统。[这是我想到的网站。](https://assertnotmagic.com/odin-restaurant/)(以及相关的 [GitHub 回购](https://github.com/rpalo/odin-restaurant)

[![My Odin Project Restaurant](img/3701b4c9f247550011f2468fb6a9888f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---KCDoir7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assertnotmagic.com/img/odin-restaurant.png)

我没有做任何事情让它在手机上看起来漂亮，所以如果你在手机上阅读，请原谅我。

## 技巧

我想分享的技术是我用于导航按钮点击回调的技术:我创建了一个闭包！让我倒回去。我有三个按钮。HTML 最终看起来像这样:

```
<div class="tabs">
  <button class="tabs__link active" data-target="About">About</button>
  <button class="tabs__link" data-target="Menu">Menu</button>
  <button class="tabs__link" data-target="Contact">Contact</button>
</div> 
```

然后我有一堆包含标签内容的`<div class="tabcontent">`。除了活动的，其他的都有`display: hidden`，所以只有活动的才会出现。

当然，作业特别要求我用 JavaScript 生成这些按钮，所以最终看起来更像这样:

```
// Don't worry about openTab now.
// We'll talk about it in a minute.
import openTab from './openTab';

const loadNav = () => {
  const tabHolder = document.querySelector('.tabs');
  const tabs = ['About', 'Menu', 'Contact'];
  tabs.forEach(tabName => {
    const button = document.createElement('button');
    button.classList.add('tabs__link');
    button.dataset.target = tabName;
    button.addEventListener('click', openTab(tabName));
    button.innerHTML = tabName;
    tabHolder.appendChild(button);
  });
}; 
```

但神奇的事情发生了。我先给你看一下`openTab`的代码，然后再来说说它有什么特别之处。

```
const openTab = tabName => {
  return (e) => {
    const tabContent = document.querySelectorAll('.tabcontent');
    tabContent.forEach(tab => {
      tab.style.display = "none";
    });

    const tabLinks = document.querySelectorAll('.tabs__link');
    tabLinks.forEach(link => {
      link.classList.remove('active');
    });

    const activeTab = document.querySelector(`[data-page="${tabName}"]`);
    activeTab.style.display = "block";
    e.currentTarget.classList.add('active');
  };
};

export default openTab; 
```

### 那么这到底是怎么回事呢？

通常，当你传递一个回调函数给一个事件监听器时，你是不带括号的，就像这样:`button.addEventListener('click', doTheThing)`。这是因为您在创建事件侦听器时没有调用函数，而是传递了函数对象供以后调用。但是，您是否曾经想要将附加信息传递给回调函数？通常当你有一个事件监听器的回调函数时，他们只把事件作为一个参数:

```
const doTheThing = e => {
  // stuff
}; 
```

但是，如果您希望它包含额外的信息呢？

```
const doTheThing = (e, myColor) => {
  console.log(myColor);
}; 
```

在我的例子中，我想写一个回调函数，用于所有三个导航按钮，即使它们的功能会有一点不同，这取决于它们试图操作的标签。所以我需要这样的东西:

```
const openTab = (e, tabName) => {
  // The stuff
}; 
```

但是，如果您尝试这样做，JavaScript 就会变得暴躁。那么我们能做什么呢？一种解决方案是在添加事件侦听器时创建一个闭包。

```
const openTab = tabName => {
  return e => {
    // Things in here have access to tabName *and* e
  }
} 
```

你这样用的时候:

```
button.addEventListener('click', openTab(tabName)); 
```

函数`openTab`立即得到*的求值，新的匿名函数作为回调被给出。和写字一样:* 

```
button.addEventListener('click', e => {
  console.log(tabName + "Haha!");
}); 
```

感谢我们的朋友 closure，放置在事件监听器之后的匿名函数保留了对`tabName`变量的访问，即使该函数在事件触发之前很久就被调用了。如果你不确定什么是闭包，一定要看看[我关于闭包](https://assertnotmagic.com/2018/02/10/closure-i-hardly-know-her/)的帖子。好处是你可以把`openTab`逻辑拉到它自己的函数中，你的`addEventListener`调用看起来会干净很多。

所以，下一次你想让你的回调函数包含更多的信息，而不仅仅是传入的事件，考虑使用闭包来解决这个问题。

## 谵妄免责

在我写这篇文章的时候，我注意到我应该在我的原始代码中修改和修正很多东西(变量名一致性，CSS 类名一致性，等等)。).我还注意到，我可能已经完全省略了`tabName`变量，并从传递给函数的`event`中获得了我所需要的一切。整个结束的事情可能是不必要的。

我将继续把这归咎于这样一个事实，即当我读到代码的这一部分时，我已经被我正在做的所有事情和我正在学习的新东西弄得神志不清了。现在我已经睡了一会儿，过去的我的代码让我有点畏缩。不好意思！

也就是说，这是我第一次真正尝试现代 JavaScript。因此，如果你看到我可以改进我的代码或做一些更习惯的事情的方法，我很乐意得到你的反馈。绝对分享你的智慧！

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/03/01/closures-and-callbacks/)**