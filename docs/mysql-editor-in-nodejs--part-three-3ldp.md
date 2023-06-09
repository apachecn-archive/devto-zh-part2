# node.js 中的 mysql 编辑器第三部分

> 原文：<https://dev.to/link2twenty/mysql-editor-in-nodejs--part-three-3ldp>

# MySQL 编辑器中的 NodeJS

我错过了一周，很抱歉，但现在我回来了。这次没做太多。我已经做了一个 JS 路由器，或者至少我认为你会这样称呼它，来处理不同的“状态”,我也已经开始制作一个真正的编辑器来选择你的数据库和表。

如果你想跟随我的旅程，这是我的[帖子索引](https://dev.to/link2twenty/adventures-of-a-hobbyist--index-4oj7)。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[ignis-pwa](https://github.com/ignis-pwa)/[node tree](https://github.com/ignis-pwa/nodetree)

### 一个用 NodeJS 写的 MySQL 浏览器

<article class="markdown-body entry-content container-lg" itemprop="text">

# 节点树

一个用 NodeJS 写的 MySQL 浏览器

在 [dev.to](https://dev.to/link2twenty/mysql-editor-in-nodejs--part-one-42j0) 上阅读该项目

试玩一下[演示](http://nodetree-mysql.herokuapp.com/)密码是`demopassword`

</article>

[View on GitHub](https://github.com/ignis-pwa/nodetree)

## 界面更新

[![interface](img/152d7d4de603a381b2d823123010d211.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k2X3hztE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2fhic31nkdxuhhq228hq.gif)

希望你能看到，我已经添加了一个新的“状态”,这将引入更多的 HTML 来覆盖已经存在的内容，但我将在稍后进行讨论。

右边的空间将有一个结果表和查询输入框。

这里有一个选择菜单仔细看看

[![menu](img/b5243a4636334803809b3b1ab9beaf3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WaZlW9U9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a9y5720fgww1ftbtcoe0.gif)

## JS 路由器

我还是不确定这算不算路由器。它有一个状态，并根据状态将不同的 HTML 文件加载到页面的主体中。

```
class SimpleStateRouter {
    constructor() {
      this.body = document.body;
      this.stateList = ['connection', 'database'];
      this.event = new CustomEvent('state-changed');
      this.req = new XMLHttpRequest();
      this.body.addEventListener('state-changed', res => { this._getFragment() });
      this.req.addEventListener('load', res => { this._setFragment(res) });

      this.body.dataset.state || this.setState('connection');
    }
    //// PRIVATE ////
    /* Private method to get fragment */
    _getFragment() {
      for (let state of this.stateList) this._unloadScript(`/scripts/${state}.js`);
      this.req.open("GET", `/fragment/${this.state}`);
      this.req.send();
    }
    /* Private method to load external JS file */
    _loadScript(url) {
      const head = document.getElementsByTagName('head')[0];
      const script = document.createElement('script');
      script.type = 'text/javascript';
      script.src = url;
      head.appendChild(script);
    }
    /* Private method to remove external JS file */
    _unloadScript(url) {
      for (let tag of document.querySelectorAll(`script`)) tag.getAttribute('src') == url && tag.parentNode.removeChild(tag)
    }
    /* Private method to update the dom */
    _setFragment(res) {
      document.body.innerHTML = res.srcElement.response;
      this._loadScript(`/scripts/${this.state}.js`);
    }
    //// PUBLIC ////
    /* Public setter method to update current state */
    setState(state = "") {
      if (this.state == state || this.stateList.indexOf(state) == -1) return
      this.state = state;
      this.body.dataset.state = this.state;
      this.body.dispatchEvent(this.event);
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

我不知道这是不是一个好主意，它似乎足够快了，我一直在关注我的灯塔得分，也不知道这是否有用，目前它都是绿色的。

[![lighthouse](img/6d25ff4704788f7b2d1b062e21e6de90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9cdpCnIa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tek7pnv7cfio6h47an6b.png)

这个状态路由器背后的想法是改变页面而不更新它，我知道这意味着后退按钮现在已经失效，所以我可能会在下周查看。

语法非常简单，我只需调用`setState`并将州名作为参数，其余的由类处理。

```
const stateHelper = new SimpleStateRouter();
stateHelper.setState('database'); 
```

Enter fullscreen mode Exit fullscreen mode

## 其他小比特

我还做了一些其他的小事情，比如添加一个材质吐司类和它的样式。我的 git 库现在是最新的，所以请随意查看，如果你愿意，可以留下问题和建议。

## 帖子结束

感谢阅读，请不要羞于评论，即使只是告诉我我做得都不对😅

再次感谢🦄🦄🦄