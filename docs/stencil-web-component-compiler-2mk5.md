# 蜡纸...Web 组件编译器？

> 原文：<https://dev.to/tunaxor/stencil-web-component-compiler-2mk5>

# 上下文

所以这对我来说是一个相当大的惊喜，我正在寻找 web 组件解决方案，因为多年来人们一直承诺它们是 web 的未来。对，在纸上(Ba Dum...聚合物有人吗？tsss...)都挺牛逼的，最后那个*该死的* `date-picker`我到处都能用，没有更多的框架，最后 html，css，javascript！

嗯，我有点错了...我首先玩的是聚合物，我甚至试图基于聚合物和网络组件技术制作我自己的[自举](https://dev.toas%20a%20practice,%20experiment)，但最终失败得很惨...因为我见过的 web 组件最大的痛点之一是`style sharing`，这是理所当然的！因为它们被设计成封装的，所以没有人会乱用`date-picker`风格！(我将继续使用一个虚构的`date-picker`。

因此，设计 web 组件也是为它们设计公共 API，这本身并不坏，但我们多年来一直在使用共享风格进行 web 开发，现在我们有了封装，有时会感觉怪异和不自然！

是的，当然，当我用面向对象语言设计一个`class`时，我希望有`public`、`protected`和`private`字段、属性或方法，每个人都希望我这样做。

现在 [Polymer](https://www.polymer-project.org) 对于 Web 组件来说是一个很好的选择，但是最终你还是做了 [Polymer](https://www.polymer-project.org) 而不是 javascript，我知道是 sugar 阻止了你在底层 API 上做定制元素，影子 Dom 之类的东西。但不知何故，我也希望它不那么聚合物化。其中一个聚合点是走向全 web 组件，所以你似乎无法访问自定义元素[，这与 web 组件](https://softwareengineering.stackexchange.com/questions/289038/what-is-the-difference-between-web-components-and-custom-elements/289039)不同，这是一个缺点，因为目前对 Web 组件的支持，不要误会，他们现在的支持甚至比去年更好，但让我来解释一下。

既然你对我的实验和经验有所了解，让我们开始吧。

# 模板

Stencil 在他们的网站上有一个非常有趣的措辞:

> 神奇的、可重用的 web 组件编译器

`Web Component Compiler`...嗯（表示踌躇等）...我的 JavaScript 感觉很兴奋，我在那句话里没有看到`Framework`。
模板是由[离子](https://ionicframework.com)团队制作的，他们使用了一系列[反应](https://reactjs.org)启发的技术

*   虚拟 DOM
*   异步渲染(受 React Fiber 启发)
*   反应式数据绑定
*   打字稿(我知道这不是反应)
*   JSX

我自己没有用过 react beyond a hello world，它对我没有吸引力，很抱歉它不适合我，是吗？

Stencil 专注于制作`Custom Elements`，取自他们的文档

> Stencil 是一个生成 Web 组件(更确切地说，是定制元素)的编译器。

这是默认情况下不启用阴影 dom，自定义元素缺少阴影 dom，这意味着它的样式没有封装！，所以你可以有一个定制的 bootstrap 主题的组件，或者 bulma 主题的组件，如果你有各种共享 css 框架的项目，Stencil 在那个领域可能对你很有吸引力。

所以到现在为止，我们知道 Stencil 默认不做 web 组件，更像是定制元素(还记得我说的聚合物走向全 Web 组件吗？)
它看起来像一个模板组件吗？

```
import { Component, Prop } from "@stencil/core";

@Component({
  tag: 'tun-login-form',
  styleUrl: 'tun-login-form.css'
})
export class TunLoginForm {
  @State() email: string;
  @State() password: string;

  render() {
    (
      <form onSubmit={this.onSubmit.bind(this)}>
        <label htmlFor="email" >Email</label>
        <input type="email" value={this.email} id="email" name="email" 
         onInput={this.onInputChange.bind(this)} required />
        <label htmlFor="password">Password</label>
        <input type="password" value={this.password} id="password" name="password"
         onInput={this.onInputChange.bind(this)} required />
        <button type="submit">Login</button>
        <button type="reset">Clear</button>
      </form>
    );
  }

  onInputChange({target}) { /* ...stuff...  */ }
  async onSubmit(event: Event) { /* ...stuff...  */ }
} 
```

所以当你看到这个，你可能会说:`well you are not doing polymer'ish, but surely you are doing react'ish/stencil'ish`是的，有点。在一定程度上是的，因为当你开始管理价值观时，你就开始感受到那种自然的用法，例如，看看我们如何表演`binding`

```
<input value={this.password} onInput={this.onInputChange.bind(this)} /> 
```

我们通常的双向数据绑定在两个独立的步骤中进行管理，并在开始时标记为`@State`

我可能对此很天真，但这是我处理它的一种方式

```
onInputChange({ target }) {
  switch (target.getAttribute('name')) {
    case 'email':
      this.email = target.value;
      break;
    case 'password':
      this.password = target.value;
      break;
    default:
      console.warn('Name not Found')
      break;
  }

  if (target.validity.typeMismatch) {
    target.classList.add('is-danger');
    target.classList.remove('is-success');
  } else {
    target.classList.remove('is-danger');
    target.classList.add('is-success');
  }
} 
```

这时你开始觉得你实际上是在使用 JavaScript，而不是使用不存在的`Framework`你上一次使用 classList 是什么时候？
还是用了 HTML5 验证 API？我知道 API 不是最好的，但是它是最原始的！所有这些都不需要外部第三方库，如果你在 css 方面足够熟练，你可以使用 fetch API，使用 HTML5 验证来完成 HTML，TSX，CSS，这只是你不会每天都做的事情`[insert framework]`这很好，因为这些框架为不同的问题提供了不同的解决方案，这里的要点是，无论你使用什么框架，这应该能够在你需要/想要的任何地方使用，因为这些都被编译成本机`Custom Elements`！

也来看看`submit`函数

```
async onSubmit(event: Event) {
  event.preventDefault();
  let res;
  try {
    res = await fetch('https://myserver.com/auth/login', {
      method: "POST",
      // don't forget cors stuff when using fetch
      mode: "cors",
      headers: {
        "Content-Type": "application/json; charset=utf-8",
      },
      body: JSON.stringify({
        email: this.email,
        password: this.password
      })
    })
       .then(response => response.json());
  } catch (error) {
    return console.warn({ error });
  }
  console.log('Woohoo Login!', { res })
} 
```

所以`event`是一个提交事件，你必须阻止它，否则浏览器会重新加载！
你的`jQuery`现在在哪里啊？所以你在任何地方都看不到`element.addEventListener(/* ... */)`，但你可以看到它与本地 javascript 代码的相似之处，`frameworkless`最终你得到的是一个编译好的定制元素，只要你的浏览器支持，你可以在任何你想插入的地方插入它！

就像你可以作为一个`PWA`开始构建一个网站一样简单，你可以开始为公众消费者构建定制元素，甚至是 Web 组件，因为它就像添加`shadow: true`
一样简单

```
@Component({
  tag: 'tun-login-form',
  styleUrl: 'tun-login-form.css',
  shadow: true // <= this one
}) 
```

这就到了我谈到的样式的痛点，这不是聚合物或模板的错，这只是阴影 DOM 的工作方式，但模板在`Custom Elements`做了一个惊人的工作，而不仅仅是完整的`Web Components`，这允许我们习惯的常见共享样式。

在这一点上，我觉得像 Stencil 一样，确实让我更接近原生浏览器方法(在一定程度上)，并且它们声称有一些好东西:

> 简单的
> 使用有意设计的小工具、小 API、零配置和类型脚本支持，您就万事俱备了。
> 
> Performant
> 6kb min+gzip 运行时、服务器端渲染和原生 Web 组件的强大功能。

一个`tiny API`和一个`small runtime`，我有一个正在进行的私人项目，我不能分享细节，但我会很快公开一些东西来分享我的感受。

您也可以选择在`dist`目录中创建捆绑包，该捆绑包将被创建用于从`npm`服务中消费，您可以在这里找到更多[信息](https://stenciljs.com/docs/distribution)

### 不喜欢的东西

1.  TSX

不要误解我，TSX 很酷，但我希望他们走得更像 Aurelia 的方式，只是一个普通的 js/ts 文件和一个普通的类及其相应的 html 文件，就是这样，没有装饰的东西，直到你需要高级的东西，但我理解为什么使用 TSX 的原因，它只是适合这个项目

1.  推荐的文件结构

```
├── card
│   ├── card.scss
│   ├── card.tsx
├── card-content
│   ├── card-content.scss
│   └── card-content.tsx
├── card-title
│   ├── card-title.scss 
```

虽然我知道所有的东西最终都是组件，但当你选择 web 项目时，他们也使用这种结构，这对我来说不太合适，因为我搞不清哪些是严格意义上的组件，哪些是页面式组件(但这只是我的情况),在风格指南中，他们对此也很清楚

> 这是一个组件风格指南，由 Stencil 的核心团队创建并在内部实施，目的是标准化 Ionic 核心组件。这只能作为其他团队创建自己的风格指南的参考。根据团队的喜好随意修改。

### 总结起来

到目前为止，我很喜欢它，因为没什么可喜欢/不喜欢的，他们故意设计了一个小 API，而且这个 API 符合`Web Component`标准，不多不少，看起来工作得很好。

我将在随后的分享如何进行`parent <- child`沟通的文章中发布一些其他发现(剧透、更多装饰者和 js 原生客户事件之类的东西)

在下面的评论里分享你的想法吧！谢谢你读了这篇乱七八糟的帖子。

别忘了检查第二部分！

[![tunaxor image](img/9fc0ccc03f174352a3818a350d5c15fc.png)](/tunaxor) [## 模板化 web 组件编译器...第二部分！

### 安吉尔·穆尼奥斯 8 月 31 日 181 分钟阅读

#stencil #javascript #tsx #webcomponents](/tunaxor/stencil-the-web-components-compiler-part-2-313b)