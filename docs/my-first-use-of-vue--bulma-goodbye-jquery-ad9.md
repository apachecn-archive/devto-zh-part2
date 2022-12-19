# 我第一次使用 Vue +布尔玛(再见 JQuery？)

> 原文：<https://dev.to/catriname/my-first-use-of-vue--bulma-goodbye-jquery-ad9>

# 九头蛇

上个月，我创建了一个. Net 核心站点，尽管我试图保持我的架构严密，我的数据库和 API 交互尽可能清晰，但我觉得我的站点有一大部分需要注意:前端。

坐满筹码通常感觉像是在与九头蛇搏斗。就在我觉得我已经更好地控制了数据库控制、服务交互、依赖性这些棘手的问题时，总有至少一个问题困扰着我。对我来说，这就是 JQuery。

你的基本。NET 核心应用程序模板包含了 JQuery 验证脚本，但是似乎以后我想要的每个特性都需要我添加一个 JQuery 插件。动画？添加插件。更不用说如果我想要响应式设计，我们正在寻找 Bootstrap，这当然需要 JQuery。Javascript 错误？祝你好运找到哪个插件有问题。

结束了，就这样结束了。我需要提高我的前端游戏！

# 我的游戏计划

*   设计:[布尔玛](http://bulma.io/)
*   对于其他一切: [VueJS](https://vuejs.org)

## 为什么是布尔玛

我在网络聚会上听到这个消息已经有一段时间了，我很喜欢它的命名规则。它的文档是干净的，它的风格与 Bootstrap 非常相似，让我觉得很舒服并继续前进。我还喜欢它是纯 CSS，没有 JS！

## 为什么飞行

这只是感觉非常轻和干净。我浏览了一下指南，非常喜欢 Matt Rothenberg 的[“这是一个 Vue.js 介绍，适合那些对 jQuery 了解不多的人”](https://medium.freecodecamp.org/vue-js-introduction-for-people-who-know-just-enough-jquery-to-get-by-eab5aa193d77)。以此为参考，我开始了我在 VueJS 的第一次尝试。

# 豚鼠

## 要求

*   登录表单
*   如果用户选择我是...“经销商”，账号字段为必填项
*   如果用户选择我是...“员工”，账号字段被禁用
*   需要密码
*   修剪所有文本输入字段

## 代码

[https://codepen.io/catriname/pen/vjmGaQ](https://codepen.io/catriname/pen/vjmGaQ)

## 解释

### VueJS

首先，我将 account 字段设置为:disabled="isDisabled "，在我的 Vue 中，我将 toggle 设置为 false(对于初始加载)，然后通过返回 toggle 的值(false)来计算 isDisabled 值。

然后，我使用 v-on:change 来调用一个名为“notNeeded”的方法，并向它传递自己的值。在我的 notNeeded 方法中，我检索 select 的值，检查“E”(雇员)并相应地设置 toggle。值贯穿计算，设置“isDisabled”和启用/禁用帐户字段。

我犯了一些不可思议的错误，比如我没有在数据中声明我的变量(例如:selected:' ')并且我在 div 中添加了 v-model 而不是实际的输入，但是总的来说这是一个很好的体验！

最后，我将一个简单的 HTML required 添加到我的必填字段和 v-model.trim 中，以消除我的文本输入中的尾随空格。

## 布尔玛

我实际上只是从一个部分开始，用列将我的屏幕分成 3 个部分，然后用中间的列作为我的登录“框”。我将指南用于表单设置的其余部分，然后将所有内容封装在“浅”色的“英雄”背景(第一个 div)中。