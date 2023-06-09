# 模板化 web 组件编译器...第二部分！

> 原文：<https://dev.to/tunaxor/stencil-the-web-components-compiler-part-2-313b>

你可以在这里找到这篇文章的源代码:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[AngelMunoz](https://github.com/AngelMunoz)/[tun-stencil-sample](https://github.com/AngelMunoz/tun-stencil-sample)

### 一个模板网站的例子，也可以导出为组件库

<article class="markdown-body entry-content container-lg" itemprop="text">

# 模板应用程序启动器

Stencil 是一个使用 web 组件构建快速 Web 应用程序的编译器。

Stencil 将最流行的前端框架的最佳概念结合到一个编译时而不是运行时工具中。Stencil 采用了 TypeScript、JSX、微型虚拟 DOM 层、高效的单向数据绑定、异步渲染管道(类似于 React Fiber)和开箱即用的惰性加载，并生成了 100%基于标准的 Web 组件，这些组件可以在任何支持自定义元素 v1 规范的浏览器中运行。

模板组件只是 Web 组件，因此它们可以在任何主流框架中工作，或者根本不需要框架。在许多情况下，Stencil 可以作为传统前端框架的替代物，尽管现在浏览器中已经提供了一些功能，但是这样使用它并不是必须的。

Stencil 还在 Web 组件上启用了许多关键功能，特别是服务器端呈现(SSR ),而无需…

</article>

[View on GitHub](https://github.com/AngelMunoz/tun-stencil-sample)

和

[带模板组件的 Stackblitz Antularjs 模板](https://stackblitz.com/edit/tun-stencil-sample-usage)

网站也在这个地方发布

在上一篇文章中，我与你分享了模板是一个专注于 T1 的 T0，它使用了 T2 和其他受 T3 启发的技术

昨天我决定做一些公开的东西，这样你就可以看到我在说什么，我做了一些进一步的工作，在`firebase`上部署一个网站，并在`npm`上发布相同的网站，然后使用网站上的组件在其他网站/项目中共享和使用。

让我告诉你，我对结果感到惊讶，但让我们先从表单开始，因为这是我在上一篇文章中承诺的

## 表格和事件

在 [src/components](https://github.com/AngelMunoz/tun-stencil-sample/tree/master/src/components) 中，您会发现三个组件

1.  调谐器数据格式
2.  调谐器轮廓形式
3.  金枪鱼罐头

从这 3 点来看，`tun-navbar`在共享方面设计得很糟糕，因为它有来自 web 应用程序本身的隐式和显式数据(就像专门针对网站本身的路由),这有点像半目的(我根本不认为它会很容易共享),但这是一个你已经可以看到的问题，当你在 stencil 中使用可共享的网站组件时，你可以用插槽甚至属性来替换那些路由，这样组件就完全不依赖于你的网站，但允许它是可扩展的。

其他两个组件仅仅是没有特定目的的形式，它们的存在只是为了展示如何在模板中做事情，而不是让网站工作。

在`Frameworks`中，就像`Vue`或`Aurelia`一样，我喜欢使用`top -> down`通信，然后在子元素中产生事件，而在父元素中使用侦听器，这样我就可以在不同的上下文中使用相同的组件，只要该上下文具有相同的属性和相似的含义。

在`tun-data-form`的情况下，我们在[表单](https://github.com/AngelMunoz/tun-stencil-sample/blob/master/src/pages/forms/forms.tsx)页面
中这样使用它

```
<section>
  <h1>Data Form</h1>
  <tun-data-form edit={this.editData}></tun-data-form>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

我们正在传递一个布尔值，以了解我们是否可以编辑数据，一些网站，显示几乎可以编辑的信息，但我们需要单击一个开关/按钮，以允许我们编辑信息，我们只是在这里遵循。

在 [tun-data-form](https://github.com/AngelMunoz/tun-stencil-sample/blob/master/src/components/tun-data-form/tun-data-form.tsx) 中我们可以看到相当多的代码，但是让我们一步一步来

```
import { Component, Prop, Event, EventEmitter, State } from '@stencil/core';

@Component({
  tag: 'tun-data-form',
  styleUrl: 'tun-data-form.scss'
})
export class TunDataForm {
  @Prop() edit: boolean = false;

  @Event() submitDataForm: EventEmitter;
  @Event() resetDataForm: EventEmitter;

  @State() email: string;
  @State() phoneNumber: string;
  @State() password: string; 
```

Enter fullscreen mode Exit fullscreen mode

在第一行，我们导入我们将在组件上使用的内容，下面的代码指定了在哪里可以找到我们的自定义样式，以及哪个标签将用于这个组件。

在下一行，我们有了我们的类声明，并开始看一些代码

1.  支柱
2.  事件
3.  状态

`Prop`是一个装饰器，它让我们指定被标记的`class`属性将来自组件
的外部

```
 <tun-data-form edit={this.editData}></tun-data-form> 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，它就是我们之前在`forms.tsx`上使用的那个`edit`属性，与`Prop`和`State`的区别在于，道具是默认绑定的`one way`，不能被组件本身修改。

`Event`是一个装饰器，它允许我们将事件发送到组件的外部，最终可以像通常的形式一样被捕获`element.addEventListener('submitDataForm',() => {}, false)`

`State`是一个装饰器，它告诉我们的组件用这个标记的`class`属性将在组件内部使用，并且它们不需要被暴露。

然后我们有了渲染函数

```
render() {
    return (
      <form onSubmit={this.onSubmit.bind(this)} onReset={this.onReset.bind(this)}>
        <article class='columns is-multiline'>
          <section class='column is-half'>
            <section class='field'>
              <label class='label'>Email</label>
              <p class='control'>
                <input type='email' class='input' name='email'
                  onInput={this.onInput.bind(this)} readOnly={!this.edit} required />
              </p>
            </section>
          </section>
          <section class='column is-half'>
            <section class='field'>
              <label class='label'>Password</label>
              <p class='control'>
                <input type='password' class='input' name='password'
                  onInput={this.onInput.bind(this)} readOnly={!this.edit} required />
              </p>
            </section>
          </section>
          <section class='column is-two-thirds'>
            <section class='field'>
              <label class='label'>Phone Number</label>
              <p class='control'>
                <input type='tel' class='input' name='phoneNumber'
                  onInput={this.onInput.bind(this)}
                  readOnly={!this.edit} pattern='[+0-9]{3}[- ][0-9]{3}[- ][0-9]{3}[- ][0-9]{2}[- ][0-9]{2}' required />
              </p>
            </section>
          </section>
        </article>
        {this.edit ? <button class='button is-info is-outlined' type='submit'>Change</button> : <span></span>}
        {this.edit ? <button class='button is-primary is-outlined' type='reset'>Cancel</button> : <span></span>}
      </form>
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

正如你所猜测的，这是典型的标记代码，唯一可能与本文目的相关的代码是这几行

```
onSubmit={this.onSubmit.bind(this)} onReset={this.onReset.bind(this)}
onInput={this.onInput.bind(this)} readOnly={!this.edit} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里处理事件并设置事件的属性，我们在代码中绑定了一些属于类的函数

这类似于`onclick="myfn()"`
和最后一个相关代码:

```
onSubmit(event: Event) {
  event.preventDefault();
  this.submitDataForm.emit({
    email: this.email,
    phoneNumber: this.phoneNumber,
    password: this.password
  });
}

onReset() {
  this.resetDataForm.emit();
} 
```

Enter fullscreen mode Exit fullscreen mode

(关于`onInput`功能的用法，请查看上一篇文章)

在这一部分中，我们最后使用`this.submitDataForm`和`this.resetDataForm`，它们是我们之前标记为`@Event`的`class`属性，它们只是下面
的合成糖

```
const event = new CustomEvent('submitDataForm', { 
  detail: {
    email: this.email,
    phoneNumber: this.phoneNumber,
    password: this.password
  }
})
document.querySelector('tun-data-form').dispatchEvent(event); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们仍然在#UsingThePlatform，只是要记住，方法、函数等等的一切都与你的逻辑相关，但是一个组件依赖的东西越少，它的可移植性就越高

现在，我应该可以在任何我想使用的地方使用这个表单组件了，如果我觉得合适，我还可以传递一个属性，该属性可能包含我在使用它之前填充这些字段所需的所有内容，这只是为了使用

现在，如果我们转到[表单](https://github.com/AngelMunoz/tun-stencil-sample/blob/master/src/pages/forms/forms.tsx)页面，将会有一个包含另一个我们还没见过的装饰器的方法`@Listen()`

```
@Listen('submitDataForm')
onSubmitDataForm({ detail: { email, password, phoneNumber }, }: CustomEvent) {
  console.log(email, password, phoneNumber);
}

@Listen('resetDataForm')
onResetDataForm() {
  this.editData = false;
} 
```

Enter fullscreen mode Exit fullscreen mode

`Listen`是一个比
甜的装饰工

```
document.querySelector('tun-data-form')
  .addEventListener('submitDataForm', function onSubmitDataForm({}) {}); 
```

Enter fullscreen mode Exit fullscreen mode

看起来 Stencil 在某个地方声明东西，并以某种方式将自己添加到窗口中，但不，这完全只是幕后的 javascript，只是浏览器 API，仅此而已，我们没有使用任何类型的`framework`或`framework`特定方法、函数；它只是一个带有 API 的浏览器环境

这里的代码相当简单，它只是监听我们在 [tun-data-form](https://github.com/AngelMunoz/tun-stencil-sample/blob/master/src/components/tun-data-form/tun-data-form.tsx) 组件中触发的`submitDataForm`自定义事件(`.emit()`)。正如您所看到的，我们在 emit 中发送的属性现在可以在我们的自定义事件的`detail`属性中获得。发出这些属性后，我们现在可以开始做 ajax 的事情，要么将它发送到我们的 API，在某个地方处理它，将其存储在本地存储上，无论您想要/需要对这些信息做什么

### 奖金

到目前为止，我们有一个不依赖于自定义业务逻辑的表单，它的工作只是收集数据，并为父组件发出数据来管理它的业务逻辑。如果我们决定有其他应用程序应该使用相同的组件，该怎么办？但是，它在 angularjs 上，我打赌它不会工作。

**错了！**前往[这个地方](https://asisma-7f89d.firebaseapp.com/forms/)查看表单如何执行以及如何工作，请打开控制台，查看我们正在记录从我们触发的自定义事件中获得的内容。

在这些[文档](https://stenciljs.com/docs/distribution)
和 unpkg 的帮助下，我已经在 [NPM](https://www.npmjs.com/package/tun-stencil-sample) 发布了相同的存储库，并且创建了这个 [stackblitz](https://stackblitz.com/edit/tun-stencil-sample-usage) ，我想在这里使用我为我的网站
创建的表单(你也可以尝试一下`https://unpkg.com/tun-stencil-sample@0.0.1/dist/tun-stencil-sample.js`)

#### 现在请注意，因为当我意识到这里发生了什么时，这让我大吃一惊

在 index.html 我们有以下代码

```
<div id="app">
  <div ui-view></div>
  <hr>
  <h1>Don't forget to check the console</h1>
  <tun-profile-form edit></tun-profile-form>
  <hr>
  <tun-data-form edit></tun-data-form>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这些都是我们在以前的网站上创建的相同的形式！**无修改**:super _ ultra _ crazy _ mega _ parrot _ ever:
您暂时需要手动添加/删除`edit`属性，但在右侧您可以看到它是如何与您之前访问的网站一样工作的！

是的，但是事件处理一定很难，对吗？
**错了！**转到`app.js`，你会在最后看到下面几行

```
document.querySelector('tun-data-form')
  .addEventListener('submitDataForm', event => console.log(event.detail), false);

document.querySelector('tun-profile-form')
  .addEventListener('submitTunProfile', event => console.log(event.detail), false); 
```

Enter fullscreen mode Exit fullscreen mode

*怎么了？我就是这个意思？这意味着如果我使用 [Aurelia](https://aurelia.io/) 我会做`<tun-data-form submit-tun-profile.bind="myFn($event)"><tun-data-form>`
如果我使用 [Vue](https://vuejs.org/) 它会是`<tun-data-form @submit-tun-profile="myFn"><tun-data-form>`，这太棒了！我没有亲自尝试过，但是嘿，你有没有检查过这个模板实际上使用的是`Angular Js`？公平地说，angularjs 并不是最友好的框架，我已经在 Vue 和[中测试过一些编译过的`polymer web components`，它们工作得很好](https://stackblitz.com/edit/polymer-vue-playground)，所以我完全确定[模板](https://stenciljs.com/)也能工作。*

昨天当我完成这项工作时，我的头被炸掉了，只花了几个小时！不是几天，不是几周，不是几个月，只是我见过的几个小时。

我的心被 Stencil 占据了，我无法表达我对 Ionic 团队的工作有多么感兴趣和惊讶，他们不仅以直观的方式使所有这些工作成为可能，而且没有额外的框架。

最后，我想分享一个去年的视频，当时他们在去年的*聚合物峰会 2017* 上首次展示了 Stencil

[https://www.youtube.com/embed/UfD-k7aHkQE](https://www.youtube.com/embed/UfD-k7aHkQE)

谢谢你看了这个乱七八糟的帖子，请在下面的评论里分享你的想法！另外，非常感谢您对我与您分享的代码的任何反馈，我不是 tsx/jsx 的忠实用户，所以可能会有一些模式并不太好。