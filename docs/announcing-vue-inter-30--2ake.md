# 宣布推出 vue-inter 3.0

> 原文：<https://dev.to/egoist/announcing-vue-inter-30--2ake>

是一个专门为 Vue.js 开发的 i18n 库，只有 1kB 大小。

代码总是胜于雄辩，我举个例子:

这是你的应用入口`index.js` :

```
import Vue from 'vue'
import Inter from 'vue-inter'
import Greeting from './Greeting.vue'

Vue.use(Inter)

const inter = new Inter({
  locale: 'en',
  // Messages for other locales
  messages: {}
})

new Vue({
  inter,
  render: h => h(Greeting)
}) 
```

Enter fullscreen mode Exit fullscreen mode

通常，您会直接在视图层中为默认区域设置编写消息，因此在创建`inter`实例时，没有必要为默认区域设置定义消息。

这是你的观点`Greeting.vue` :

```
<template>
  <div>
    <format-message
      path="app.greeting"
      defaultMessage="Hello {name}!"
      :data="{name: 'egoist'}"
    />
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

用这个它将被渲染如下:

```
<div><span>Hello egoist!</span></div> 
```

Enter fullscreen mode Exit fullscreen mode

## 添加新的区域设置

首先您需要在创建`inter`实例时定义相关的消息:

```
new Inter({
  locale: urlQuery.lang || 'en',
  messages: {
    // e.g. Add `cn` for Chinese
    cn: {
      app: {
        greeting: '你好 {name}'
      }
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在访问`?lang=cn`，你会得到:

```
<div><span>你好 egoist!</span></div> 
```

Enter fullscreen mode Exit fullscreen mode

[![Edit Vue Template](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/6n0wzjvj2k)

## 复数支持

您可以使用 [intl-messageformat](https://github.com/yahoo/intl-messageformat) 获得额外的复数支持:

```
import IntlMessageFormat from 'intl-messageformat'

const inter = new Inter({
  template(message, data) {
    if (!data) return message
    const tpl = new IntlMessageFormat(message, this.currentLocale)
    return tpl.format(data)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以在本地消息中使用这样的语法:

```
<format-message
  path="app.showApples"
  defaultMessage="You have {num, plural, 
    =0 {no apples.}
    =1 {one apple.}
    other {# apples.}
  }"
  :data="{num: 10}"
/> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您可能需要使用 [polyfill `Intl`](https://github.com/yahoo/intl-messageformat#intl-dependency) 和[加载非英语语言](https://github.com/yahoo/intl-messageformat#loading-intl-messageformat-in-a-browser)的区域设置数据。

## 直接使用 API

您可以在您的组件中以`this.$inter`的名称直接访问您的 Inter 实例，例如:

```
// Switch to `cn` locale
this.$inter.setCurrentLocale('cn') 
```

Enter fullscreen mode Exit fullscreen mode

更多细节见 [API](https://github.com/egoist/vue-inter#api) 。

## 与 vue-i18n 的区别

*   vue-inter 很简单，最小，速度极快，相比之下，vue-i18n 的大小为 5kB(gzipped)
*   复数/日期时间支持在 vue-inter 中是可选的(所以才这么小！)

* * *

## 链接:

*   [视图-视图-间](https://github.com/egoist/vue-inter#api)
*   [codesandbox 演示](https://codesandbox.io/s/vvky0nov6l)
*   [我的推特@_egoistlily](https://twitter.com/_egoistlily)