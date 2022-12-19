# 使用交叉点观察器 API 构建目录

> 原文：<https://dev.to/teej/building-a-table-of-contents-with-the-intersection-observer-api-3b3g>

这最初是[发布在我的博客](https://tj.ie/building-a-table-of-contents-with-the-intersection-observer-api/)上。

在计划一篇即将发表的文章时，我注意到有相当多的内容要涵盖，却没有简单的方法来浏览。因此，我没有实际写这篇文章，而是偏离主题，构建了一个目录组件。太好了，继续写作...啊，如果它能跟随用户向下滚动页面就好了。好了，完成了。等等，如果我可以突出显示当前活动的部分，那就太酷了...

我最终得到的绝不是完美的；当你向上滚动到一个新的标题时，它不会正确地高亮显示。然而，我认为它现在做得很好，没有使用 JavaScript 将每个部分包装在一个可识别的容器中，我可以随时改进它，或者如果它不适合目的，就完全删除它。

## 设置

我正在使用 Hugo 来生成我的站点，它带有一个有用的标签来自动创建目录。其工作原理是找到所有的标题，给它们分配一个 id，并生成一个链接列表。

```
{{ .TableOfContents }} 
```

首先，我们需要一份有标题的文件。这些标题应该有一个 id，这样我们就可以使用锚标签跳转到文档的那个部分。我们还需要一个链接列表，其中的`href`指向每个标题。这个想法是，基本功能在没有启用 JavaScript 的情况下也能工作。

```
 <div>
  <aside>
    <p>Table of Contents</p>

    <ul>
      <li>
        <a href="#first-heading">First Heading</a> 
      </li>
      <li>
        <a href="#second-heading">Second Heading</a> 

        <ul>
          <li>
            <a href="#third-heading">Third Heading</a> 
          </li>
        </ul>
      </li>
    </ul>
  </div>

  <article>
    <h1>Article Title</h1>

    ... 

    <h2 id="first-heading">First Heading</h2>
    ...
    <h2 id="second-heading">Second Heading</h2>
    ...
    <h3 id="second-heading">Third Heading</h3>
    ... and so on
  </article>
</aside> 
```

导航中的列表是嵌套的，以反映内容的层次结构。h3 位于 h2 定义的区间内，h4 位于 h3 定义的区间内，以此类推...

## 编写 JavaScript 对象

我相信你们这些聪明人会做得更干净。我选择避免任何插件，以努力了解更多正在发生的事情，并保持我的脚本包大小下降。这意味着不支持交叉点观察器 API 的浏览器没有后备支持。在撰写本文时，[意味着 Internet Explorer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API#Browser_compatibility) 。

我认为第一步是收集一篇文章中的链接和标题列表。我把它包在一个对象中，试图组织代码。

```
const TableOfContents = {
  container: document.querySelector('.js-toc'), // this is the container for our links
  links: null,
  headings: null,

  init() {
    this.findLinksAndHeadings()
  },

  findLinksAndHeadings() {
    this.links = [...this.container.querySelectorAll('a')]
    this.headings = this.links.map(link => {
      let id = link.getAttribute('href')
      return document.querySelector(id)
    })
  }
} 
```

我在这里析构一个数组:`this.links = [...this.container.querySelectorAll('a')]`的原因是因为`querySelectorAll`将返回一个节点列表，而不是一个我们可以称之为`filter`、`forEach`、`map`等的数组...开着。

然后我`map`浏览链接，找到`href`并使用它来收集链接到的标题。

### 路口观察者

在我们得到交叉点观察器 API 之前，我们已经在 scroll 的窗口对象上附加了一个事件监听器。然后我们将检查页面的当前滚动位置，并根据我们想要检查的元素的`offsetTop`进行测量。我们仍然可以做到这一点，但滚动可能会很昂贵，除非我们消除听者的反跳。在任何情况下，我们都将进入 DOM 提取值，手动检查它们，然后继续。

有了这个 API，我们可以通过学习一种稍微不同的方法来检测视口中的元素，从而最大限度地减少工作量。

让我们来看看我从上面的 MDN 链接中复制的一个精简示例:

```
let options = {
  root: document.querySelector('#article'),
  rootMargin: '0px',
  threshold: 1.0
}

let observer = new IntersectionObserver(callback, options) 
```

来自 MDN:

> #### 根
> 
> 用作检查目标可见性的视口的元素。必须是目标的祖先。如果未指定或为空，则默认为浏览器视口。
> 
> #### 根缘
> 
> 根部周围的边距。可以具有类似于 CSS margin 属性的值，例如“10px 20px 30px 40px”(上、右、下、左)。这些值可以是百分比。这组值用于在计算交集之前增大或缩小根元素边界框的每一边。默认为全零。
> 
> #### 阈值
> 
> 一个数字或一个数字数组，指示观察者的回调应该在目标可见性的多少百分比上执行。如果只想检测可见性何时超过 50%标记，可以使用值 0.5。如果您希望每次可见度超过 25%时都运行回调，那么您可以指定数组[0，0.25，0.5，0.75，1]。默认值为 0(意味着只要有一个像素可见，回调就会运行)。值为 1.0 意味着在每个像素都可见之前，不会认为阈值已过。

有了这些信息，我使用了以下选项:

```
{
  rootMargin: '0px',
  threshold: 1
} 
```

我省略了`root`,因为我希望它默认为浏览器视窗。我不需要来自`rootMargin`的任何额外的东西，并且`threshold`在元素 100%可见时激活是有意义的。

对于观察器本身，我需要为回调编写一个函数，并观察标题何时可见。

```
this.observer = new IntersectionObserver(
  this.handleObserver,
  this.intersectionOptions
)

this.headings.forEach(heading => {
  this.observer.observe(heading)
})

handleObserver(entries, observer) {
  entries.forEach(entry => {
    let href = `#${entry.target.getAttribute('id')}`,
        link = this.links.find(l => l.getAttribute('href') === href)

    // I needed to use > 1 on Codepen, not sure why?
    if (entry.isIntersecting && entry.intersectionRatio === 1) {
      link.classList.add('is-visible')
      this.previousSection = entry.target.getAttribute('id') // I'll tell you in a minute
    } else {
      link.classList.remove('is-visible')
    }

    this.highlightFirstActive() // in a minute I said
  })
} 
```

因此，我创建了观察者，观察每个方向，并检查是否有任何方向在视图中。如果它在视图中，我给它一个类，`is-visible`，否则，我删除它。上下滚动页面，现在我可以通过链接上的类看到当前正在查看的标题。现在来设计一个最合适的。

### 查找活动截面

这是一次非常有趣的 T2 之旅。不吃早餐是我的错，过了这么久才想明白。虽然多个标题可能同时可见，但我只希望第一个可用的标题显示活动状态。这对于`document.querySelector`来说足够简单，它将返回找到的第一个匹配。我遇到的问题是，一旦标题消失，即使我在文档的相关部分，活动样式也会被删除。

突破时刻是跟踪先前活跃的部分。有了这个，我可以说“好的，如果现在没有活动的，默认为最近活动的部分。”这就是`this.previousSection`正在为我们做的事情。

```
highlightFirstActive() {
  let firstVisibleLink = this.container.querySelector('.is-visible')

  this.links.forEach(link => {
    link.classList.remove('is-active')
  })

  if (firstVisibleLink) {
    firstVisibleLink.classList.add('is-active')
  }

  if (!firstVisibleLink && this.previousSection) {
    this.container.querySelector(
      `a[href="#${this.previousSection}"]`
    ).classList.add('is-active')
  }
} 
```

我抓取第一个可见的链接，然后移除所有其他的`is-active`类。在这之后，我检查我们是否真的有一个可见的部分(我们可能在没有可见标题的部分之间)。如果我们找到一个，太好了；加上阶级和保释。如果我们找不到一个，而我们有一个先前的部分，那么这就是我们突出显示的。

## 演示

参见 [CodePen](https://codepen.io) 上 T.J. Fogarty ( [@tjFogarty](https://codepen.io/tjFogarty) )的笔[卷轴间谍目录](https://codepen.io/tjFogarty/pen/yxPdqe/)。

## 到此为止

如果我说这是经过深思熟虑的，那我就是在撒谎。这是切线中的切线。我在这篇文章上花的时间比我写代码的时间还多，我肯定我错过了一些可以大大简化它的东西。如果你发现了什么，叫我一声，我会修改的。

在最终版本中，我还引入了项目平滑滚动，只要用户不介意检查`prefers-reduced-motion`媒体查询。