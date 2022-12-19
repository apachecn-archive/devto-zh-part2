# node.js 中的 mysql 编辑器第二部分

> 原文：<https://dev.to/link2twenty/mysql-editor-in-nodejs--part-two-2abm>

# MySQL 编辑器中的 NodeJS

本周我继续为 MySQL 查询浏览器制作界面。您现在可以存储会话，尽管还没有实现任何东西来与 MySQL 进行实际的交互，甚至测试连接。如果你想跟随我的旅程，这是我的[帖子索引](https://dev.to/link2twenty/adventures-of-a-hobbyist--index-4oj7)。

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png)[ignis-pwa](https://github.com/ignis-pwa)/[node tree](https://github.com/ignis-pwa/nodetree)

### 一个用 NodeJS 写的 MySQL 浏览器

<article class="markdown-body entry-content container-lg" itemprop="text">

# 节点树

一个用 NodeJS 写的 MySQL 浏览器

在 [dev.to](https://dev.to/link2twenty/mysql-editor-in-nodejs--part-one-42j0) 上阅读该项目

试玩一下[演示](http://nodetree-mysql.herokuapp.com/)密码是`demopassword`

</article>

[View on GitHub](https://github.com/ignis-pwa/nodetree)

## 界面怎么样？

我已经去了一个材料设计灵感的外观与适应性设计，在过去我曾使用 web 组件，但我决定与这个项目，至少现在，我会用 HTML，CSS 和 JavaScript 自己做一切。

这里快速浏览一下目前为止的界面。

**响应:**

[![responsive](../Images/b07ffe70e0b9fb1db7ef8990be3c8322.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z4yjDZKz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4ep4arnru0k6acqunhne.gif)

**新增(手机):**

[![add new](../Images/1021d6647006ff9ba8248e0c5d87e774.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---euDl7P8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wcck9ri44hd413e21q95.gif)

**删除(桌面):**

[![delete](../Images/625eb5b59e662541e814d03952a34ab3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xjxy2z-c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pzpgehovwyqdgodd3yl8.gif)

## 学到什么了吗？

我想出了一个方法来创建一个确认模态，虽然我肯定其他人已经这样做了，或者出于某种原因这是一个坏主意，我认为这很有趣。

**Javascript:**

```
class ShowHide {
  constructor(selector) {
    this.element = document.querySelector(selector);
  }
  show() {
    this.element.hasAttribute('hidden') && this.element.removeAttribute('hidden');
  }
  hide() {
    this.element.hasAttribute('hidden') || this.element.setAttribute('hidden', '');
  }
}

class ModalController extends ShowHide {
  constructor(selector, confirmModal = false) {
    super(selector);
    this.confirmModal = confirmModal;
    this.hide();
  }
  confirm() {
    let confirmObj = new CustomEvent('confirm', { detail: true });
    this.element.dispatchEvent(confirmObj);
    this.hide();
  }
  reject() {
    let confirmObj = new CustomEvent('confirm', { detail: false });
    this.element.dispatchEvent(confirmObj);
    this.hide();
  }
  confirmPromise() {
    if (!this.confirmModal) return false;
    this.show();
    return new Promise((res, rej) => {
      this.element.addEventListener('confirm', (e) => {
        if (e.detail) { res() } else { rej() }
      }, { once: true })
    })
  }
}

const confirmationB = new ModalController('.modal.conf', true);
confirmationB.element.querySelector('.backdrop').addEventListener('click', () => { confirmationB.reject() });
confirmationB.element.querySelector('.modal-reject').addEventListener('click', () => { confirmationB.reject() });
confirmationB.element.querySelector('.modal-confirm').addEventListener('click', () => { confirmationB.confirm() }); 
```

Enter fullscreen mode Exit fullscreen mode

**HTML:**

```
<div hidden class="modal conf">
  <div class="backdrop"></div>
  <div class="content shadow">
    <div class="header">Confirmation</div>
    <div class="central">Are you sure?</div>
    <div class="footer">
      <button class="modal-reject">No</button>
      <button class="modal-confirm">Yes</button>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

当然，也有一些 CSS 样式，如果你有兴趣看的话，它们会被上传到上面链接的 GitHub 页面。

有了这个，我们现在可以运行这样的东西。

```
confirmationB.confirmPromise().then(()=>{
  console.log('The user pressed yes.');
}).catch(()=>{
  console.log('The user pressed no or outside the modal box.');
}) 
```

Enter fullscreen mode Exit fullscreen mode

我实现它是为了确认用户是否真的想要删除一个会话。

[![confirm](../Images/de4a5c2d0ae2c4ecabb893e4183224bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q1U-j36T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3vflsdhzklc07mklr2f7.png)

## 接下来是什么？

下周我将着眼于使它功能化，我的目标是让它显示一个可用表的列表，并在一个表中显示来自一个查询的数据。

我的下一篇文章将会有更多的 UI 元素，我确定，我还没有为显示表格做 UI，但是我已经有了一些想法，你喜欢 gif 吗，或者它们有点烦人？

如果你想看看我的知识库，并指出我可以做得更好的事情，那将是惊人的，我会把它纳入下周的东西。

## 帖子结束

一如既往，非常感谢阅读❤
这个项目到目前为止一直很容易，但请放心，我相信我们很快就会遇到减速和一些复杂情况😀

🦄🦄🦄