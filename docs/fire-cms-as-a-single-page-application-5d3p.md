# 将 CMS 作为单页应用程序启动

> 原文：<https://dev.to/megazear7/fire-cms-as-a-single-page-application-5d3p>

在本系列的[上一篇文章](https://www.alexlockhart.me/2018/08/the-fire-cms-paradigm.html)中，我解释了如何使用 [Firebase](https://firebase.google.com/) 主机和 [Firestore](https://firebase.google.com/docs/firestore/) 来定义网站的多语言内容层次。然后我们使用 [lit-html](https://polymer.github.io/lit-html/) 来呈现这些内容。现在，我将演示如何将它组织成一个组件模型，然后将单页面应用程序(SPA)的各个方面添加到这个方案中。

你会想熟悉[上一篇文章](https://www.alexlockhart.me/2018/08/the-fire-cms-paradigm.html)中的代码片段，因为我们现在将扩展它们。

## 抽样资料

在我们开始之前，参考[之前的帖子](https://www.alexlockhart.me/2018/08/the-fire-cms-paradigm.html)在 [Firestore](https://firebase.google.com/docs/firestore/) 中设置样本数据。然后扩展它，如下所示。我们将有三个页面，每个页面都有一个标题、描述和路径属性，以及一个内容属性，这是一个内容数组，我们将使用它来构建每个页面的内容。

#### /en/us/home

```
{  "title":  "The Home Page",  "description":  "This is the home page",  "path":  "/en/us/home",  "type":  "home",  "content":  [  "A simple home page."  ]  } 
```

* * *

#### /en/us/home/example

```
{  "title":  "Example",  "description":  "This is an example page",  "path":  "/en/us/home/example",  "content":  [  "Some sample content",  {  "type":  "header",  "text":  "A sample header"  },  {  "type":  "image",  "src":  "<some image url>"  }  ]  } 
```

* * *

#### /en/us/home/anotherPage

```
{  "title":  "Another Page",  "description":  "This is another page",  "path":  "/en/us/home/anotherPage",  "content":  [  {  "type":  "header",  "text":  "A sample header"  },  "First paragraph",  "Second paragraph"  ]  } 
```

## 组件模型

在[之前的帖子](https://www.alexlockhart.me/2018/08/the-fire-cms-paradigm.html)中，我们有一个 getPath 函数，允许我们使用当前页面的位置来获取与该页面相关的数据。我们将把这个概念扩展到路由器组件和基本页面组件，如下所示。这使用了我之前讲过的[香草 Javascript 组件模式](https://www.alexlockhart.me/2018/07/the-vanilla-javascript-component-pattern.html)，但是也使用了 [lit-html](https://polymer.github.io/lit-html/) 进行模板化。

#### 路由器

```
import {html, render} from '/vendor/lit-html.js';
import BasicPage from '/components/BasicPage.js';

export default class Router {
  init(container) {
    this.container = container;

    this.render();
  }

  render() {
    render(Router.markup(this), this.container);
    this.pageComponent = new BasicPage(
        this.container.querySelector('.page'),
        Router.getPath(window.location.pathname));
  }

  static markup({}) {
    return html`<div class="page"></div>`;
  }

  static getPath(path) {
    const truePath = ['', '/'].includes(path) ? '/us/en/home' : path;
    const parts = truePath.substring(1).split('/')
    let query = db.collection('pages');
    parts.forEach((part, index) => {
      query = query.doc(part);
      if (index !== parts.length -1) query = query.collection('children');
    });
    return query;
  }

  constructor(container) {
    // The constructor should only contain the boiler plate 
    // code for finding or creating the reference.
    if (typeof container.dataset.ref === 'undefined') {
      this.ref = Math.random();
      Router.refs[this.ref] = this;
      container.dataset.ref = this.ref;
      this.init(container);
    } else {
      // If this element has already been instantiated,
      // use the existing reference.
      return Router.refs[container.dataset.ref];
    }
  }
}

Router.refs = {};

document.addEventListener('DOMContentLoaded', () => {
  new Router(document.getElementById('router'))
}); 
```

如果你熟悉普通 Javascript 组件模式，我们在这里做的第一件事是实例化一个 BasicPage 类型的子组件，我们将在后面的文章中定义它。此外，我们还添加了一个 getPath 方法，我们已经在本系列的[上一篇文章](https://www.alexlockhart.me/2018/08/the-fire-cms-paradigm.html)中定义了它。

#### 基本页面

```
import {html, render} from '/vendor/lit-html.js';

export default class BasicPage {
  init(container, pageQuery) {
    this.container = container;
    this.pageQueryValue = pageQuery;
    this.render();
  }

  render() {
    render(BasicPage.markup(this), this.container);
  }

  set pageQuery(page) {
    this.pageQueryValue = page;
    this.render();
  }

  get pageQuery() {
    return this.pageQueryValue;
  }

  static markup({pageQuery}) {
    return html`
      <div class="content"> ${pageQuery.get().then(page => html`
          <p class="description">${page.data().description}</p>
        `)} </div>
      <div class="nav"> ${pageQuery.get().then(page => html` ${page.data().title} `)} </div>
    `;
  }

  constructor(container, doc) {
    // The constructor should only contain the boiler plate
    // code for finding or creating the reference.
    if (typeof container.dataset.ref === 'undefined') {
      this.ref = Math.random();
      BasicPage.refs[this.ref] = this;
      container.dataset.ref = this.ref;
      this.init(container, doc);
    } else {
      // If this element has already been instantiated,
      // use the existing reference.
      return BasicPage.refs[container.dataset.ref];
    }
  }
}

BasicPage.refs = {}; 
```

这里我们有另一个相当简单的类。它定义了 pageQuery getter 和 setter，并在 init 方法中初始化了 pageQuery。这是一个 [Firebase](https://firebase.google.com/) 承诺，将返回我们的页面数据。如果回顾路由器，这个 pageQuery 是使用 getPath 方法创建的。在这个值上设置一个 setter 将允许路由器在用户导航时重新呈现页面。

## 添加导航

在这篇文章的开头，我们在 Firestore 中创建了三个页面，但是我们目前没有在它们之间建立链接。让我们向 BasicPage 组件添加一些导航。

```
export default class BasicPage {
  ...  

  get homePageQuery() {
    return (async () => {
      let pageQuery = this.pageQuery;
      let type = (await pageQuery.get()).data().type;
      while (type != 'home') {        pageQuery = pageQuery.parent.parent;
        type = (await pageQuery.get()).data().type;
      }

      return pageQuery;
    })();
  }

  get navPagesQuery() {
    return this.homePageQuery
    .then(homePageQuery => homePageQuery.collection('children').get())
    .then(children => {
      let firstLevelPages = [];
      children.forEach(child => {
        firstLevelPages.push(child.data());
      });
      return firstLevelPages
    });
  }

  static markup({pageQuery, homePageQuery, navPagesQuery}) {
    return html`
      <div class="content"> ${pageQuery.get().then(page => html`
          <p class="description">${page.data().description}</p>
        `)} </div>
      <div class="nav">
        <div class="current"> ${pageQuery.get().then(page => html` ${page.data().title} `)} </div>
        <div class="links"> ${homePageQuery.then(homePageQuery => homePageQuery.get())
                         .then(homePage => html`
            <a href="${homePage.data().path}"
               class="${homePage.data().path === window.location.pathname
                        ? 'active' : ''}">${homePage.data().title}</a>
          `)}  ${navPagesQuery.then(navPages => navPages.map(navPage => html`
            <a href="${navPage.type === 'redirect'
                       ? navPage.redirect : navPage.path}"
               target="${navPage.type === 'redirect'
                         ? '_blank' : ''}"
               class="${navPage.path === window.location.pathname
                        ? 'active' : ''}">${navPage.title}</a>
          `))} </div>
      </div>
    `;
  }

  ...
} 
```

这里我们简单地添加了一个 homePageQuery getter 方法，它使用 PageQuery，然后创建一个新的查询来获取主页。我们还添加了一个 navPagesQuery，它使用 homePageQuery 为主页下的每个页面获取一个新的查询。最后，标记方法被更新以利用这些值。

## 呈现页面内容

```
export default class BasicPage {
  ...

  renderComponent(comp) {
    if (typeof comp === 'string') {
      return html`<p>${comp}</p>`;
    } else if (comp.type === 'image') {
      return html`<img src="${comp.src}"></a>`;
    } else if (comp.type === 'header') {
      return html`
        <hr>
        <h3>${comp.text}</h3>
      `;
    }
  }

  static markup({pageQuery, homePageQuery, navPagesQuery, renderComponent}) {
    return html`
      <div class="content"> ${pageQuery.get().then(page => html`
          <p class="description">${page.data().description}</p>
          <hr> ${page.data().content ? page.data().content
            .map(comp => renderComponent(comp)) : ''} `)} </div>
      <div class="nav">
        <div class="current"> ${pageQuery.get().then(page => html` ${page.data().title} `)} </div>
        <div class="links"> ${homePageQuery.then(homePageQuery => homePageQuery.get())
                         .then(homePage => html`
            <a href="${homePage.data().path}"
               class="${homePage.data().path === window.location.pathname
                        ? 'active' : ''}">${homePage.data().title}</a>
          `)}  ${navPagesQuery.then(navPages => navPages.map(navPage => html`
            <a href="${navPage.type === 'redirect'
                       ? navPage.redirect : navPage.path}"
               target="${navPage.type === 'redirect'
                         ? '_blank' : ''}"
               class="${navPage.path === window.location.pathname
                        ? 'active' : ''}">${navPage.title}</a>
          `))} </div>
      </div>
    `;
  }

  ...
} 
```

这里我们简单地添加了一个方法，该方法接受一个字符串或一个对象，并根据类型属性返回一个 [lit-html](https://polymer.github.io/lit-html/) 模板。图像返回一个图像标签，标题返回一个 h3 标签，字符串返回一个段落。然后将该方法提供给标记方法，迭代当前页面的内容属性，并将每个值提供给 renderComponent 方法进行呈现。

## 单页应用程序

现在，为了使这成为一个单页面应用程序，我们需要捕获导航事件，并覆盖行为。路由器将简单地使用 getPath 方法在我们的 BasicPage 组件上设置 pageQuery，而不是对页面进行完全刷新。

```
export default class Router {
  init(container) {
    this.container = container;

    document.onclick = (e) => {
      e = e ||  window.event;
      var element = e.target || e.srcElement;

      if (element.tagName == 'A' &&
          element.getAttribute('href').startsWith('/')) {
        window.history.pushState({}, null, element.getAttribute('href'));
        this.pageComponent.pageQuery =
            Router.getPath(window.location.pathname);
        return false;
      }
    };

    this.render();
  }

  ...
} 
```

记住 getPath 是一个静态方法，所以我们必须使用类名来引用它。此外，它返回当前位置的页面数据承诺。这个值的 BasicPage classes set 方法强制重新呈现视图，所以现在我们应该在浏览站点时看到更新的视图，而不需要刷新整个页面。此外，我们在这个设置中免费获得深度链接，因此 URL 可以指向特定的页面，用户不会像某些 SPA 那样总是被发送到开始屏幕。

## 总结想法

现在，我们有了 Fire CMS 的数据模型、客户端渲染和功能的组件模型，以及一些用于将其实现为单页面应用程序的基本链接覆盖，这样用户就可以在不刷新页面的情况下进行导航。只有我们目前需要的数据才通过网络发送。这将带来非常快速和愉快的用户体验。下一步是将这个概念扩展到一个渐进式的网络应用程序中，这样用户就可以在现代平台上将我们的网站作为一个应用程序来安装。我们将在以后的博客文章中讨论这最后一步。