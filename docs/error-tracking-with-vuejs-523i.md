# 使用 Vue.js 进行错误跟踪

> 原文：<https://dev.to/mostlyjason/error-tracking-with-vuejs-523i>

[Vue(读作/vjuː/，像](https://vuejs.org/v2/guide/index.html) [**视图**](https://vuejs.org/v2/guide/index.html) [)](https://vuejs.org/v2/guide/index.html) 是一个用于在 web 上构建用户界面的**渐进式框架**。Vue 可以驱动复杂的单页应用程序，通常与现代工具[和支持库](https://vuejs.org/v2/guide/deployment.html)结合使用。我们将向您展示如何在 Vue 应用程序中添加错误处理来捕获已捕获和未捕获的错误。这使您有机会恢复和更新呈现给用户的内容，并跟踪错误以确定修复的优先级。我们还将展示如何使用 Rollbar 监控生产中的错误。

[![Rollbar loves Vue.js](img/46321cb46cfab925f59be921bcd0a457.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--091mfVue--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h5puqudwgfofpm1ts8lu.png)

## 如何处理 vanilla Vue 中的错误

您可以使用基本的 [try、catch 和 finally](https://www.w3schools.com/js/js_errors.asp) 语句来捕获普通 JavaScript 中的异常。您可以使用这些语句来处理 Vue 组件中被捕获的异常。在本例中，我们只是登录到控制台。

```
try {
  JSON.parse("non-JSON data")
} catch(e) {
  console.log('Exception: ', e)
} 
```

Enter fullscreen mode Exit fullscreen mode

Vue 提供了一个标准的 API 来添加自定义的`errorHandler`。您应该在根 Vue 实例上进行配置，通常是在 main.js 文件中。但是，它仅捕获组件呈现期间发生的错误。这意味着它不会捕捉到后来由于用户行为等原因而发生的错误。

```
Vue.config.errorHandler = err => {
  console.log('Exception: ', err)
} 
```

Enter fullscreen mode Exit fullscreen mode

为了在整个页面上全局地处理错误，可以在窗口的`onerror`函数中添加一个处理程序。

```
window.onerror = function(message, source, lineno, colno, error) {
  console.log('Exception: ', error)
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这对于在开发过程中处理错误非常有用，但是当您部署到生产环境中时，您需要一种方法来集中跟踪这些错误，以确定它们如何影响用户体验。

## 用滚动条监控 Vue 错误

记录到控制台的错误在生产环境中用处不大，因为您的开发人员无权访问它们。集中监控错误非常重要，这样您就可以在更多客户受到影响之前修复它们。这有助于确定高影响错误的优先级，并更快地解决原因。

Rollbar 的 [JavaScript SDK](https://docs.rollbar.com/docs/javascript/) 可以让您跟踪和分析 Vue 应用程序中发生的错误，包括详细的堆栈跟踪、请求参数、用户行为遥测、受影响的用户等等。这有助于开发人员快速识别和修复错误。了解更多关于[滚动条的 JavaScript 特性](https://rollbar.com/error-tracking/javascript/)。

下面，你可以看到我们已经创建了一个[示例应用程序](https://github.com/RollbarExample/Rollbar-Vue-Example)，当用户点击一个按钮时它会触发一个异常。在滚动条中跟踪错误信息，包括一个堆栈跟踪，在这里可以看到导致错误的代码行。滚动条捕捉应用程序中任何地方发生的错误。

[![Screenshot of Rollbar Vue Error](img/6fe01af91adf43923cf7428f0eeee2e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cROvynV9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8lutehzjylznm03orlbl.gif)

## 如何在滚动条上设置 Vue 项目

1.  访问 https://rollbar.com，如果你还没有注册账户，就注册吧。接下来，创建您的项目，并从通知列表中选择 Other。选择为您生成的客户端访问令牌。在下面的步骤中，您将需要它来配置 Rollbar。

2.  要通过 npm 在您的项目中安装 Vue SDK，请打开项目根目录中的命令提示符并运行下面的命令。

```
npm install vue-rollbar --save 
```

Enter fullscreen mode Exit fullscreen mode

## 在错误处理程序中添加滚动条

要将 Rollbar 添加到 Vue 应用程序中，您需要遵循一些简单的步骤。

1.  在 main.js 文件中添加 Rollbar。您可以在项目根目录的 src 文件夹下找到 main.js 文件。

```
var Rollbar = require('vue-rollbar'); 
```

Enter fullscreen mode Exit fullscreen mode

1.  接下来，您需要使用带有访问令牌和一些可选参数的 Rollbar。这里我们已经将`captureUncaught`设置为 true，所以我们甚至不需要将事件处理程序连接到`onerror`函数。Rollbar 会自动完成这项工作。

```
Vue.use(Rollbar, {
     accessToken: 'ACCESS-TOKEN’',
     captureUncaught: true,
     captureUnhandledRejections: true,
     enabled: true,
     source_map_enabled: true,
     environment: 'production',
     payload: {
       client: {
            javascript: {
               code_version: '1.0'
            }
       }
     }
}); 
```

Enter fullscreen mode Exit fullscreen mode

1.  最后，在错误处理程序中添加 Rollbar 错误报告方法。

```
Vue.rollbar.error(err); 
```

Enter fullscreen mode Exit fullscreen mode

添加了 Rollbar 错误报告方法后，main.js 文件看起来是这样的:

```
import Vue from 'vue'
import App from './App'
import router from './router'

var Rollbar = require('vue-rollbar');

Vue.config.productionTip = false;

Vue.use(Rollbar, {
     accessToken: 'ACCESS-TOKEN',
     captureUncaught: true,
     captureUnhandledRejections: true,
     enabled: true,
     source_map_enabled: true,
     environment: 'production',
     payload: {
       client: {
            javascript: {
               code_version: '1.0'
            }
       }
     }
});

new Vue({
 el: '#app',
 router,
 render: h => h(App,Vue.config.errorHandler= err => {
   Vue.rollbar.error(err);
 })
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 上传源地图到滚动条

如果使用 Javascript，Rollbar 可以使用源代码映射将错误消息映射回原始源代码。源代码映射对于调试生产代码是必不可少的。它们将浏览器的调试输出链接回被缩小或传输前的原始源代码。为了显示原始代码的堆栈跟踪，Rollbar 需要访问您的小型 Javascript 的源映射。

要上传源地图，您需要在部署脚本中添加一个滚动条[源地图](https://docs.rollbar.com/docs/source-maps/) API 调用。下面是一个使用`curl` :
的例子

```
curl https://api.rollbar.com/api/1/sourcemap/ \
     -F access_token=’SERVER-ACCESS-TOKEN’\
     -F version=’1.0’ \
     -F minified_url=https://s3.us-east-2.amazonaws.com/rollbar-example/app.[hash].js \
     -F source_map=dist/static/js/app.[hash].js.map \
     -F App.vue=App.vue \
     -F HelloWorld.vue=HelloWorld.vue 
```

Enter fullscreen mode Exit fullscreen mode

该 API 调用中的参数是:

*   **access_token:** 滚动条上的目标项目令牌。这个令牌是在滚动条上创建项目时生成的。
*   **环境:**部署服务的部署环境。我们可以配置不同的环境，例如开发、试运行和生产。
*   **版本:**部署的应用版本。如果提供的版本是提交 ID，滚动条将创建一个到存储库提交源代码的链接。
*   **minified _ url:**minified 文件的完整 URL。它应该以 http:或 https:开头，我们将去掉它们。
*   **source_map:** 源地图的内容，作为多部分文件上传。

## 测试样本应用

为了测试它的工作情况，创建一个会产生错误消息的页面。在下面的示例中，您可以通过单击“生成错误”按钮来生成错误。

```
<template>
  <div class="hello">
    <h1>Rollbar Vue Example</h1>
    <ul>
      <li>
        <button v-on:click="parse">Generate an error</button>
      </li>
    </ul>
  </div> </template> 
<script>
  import Vue from 'vue'
  export default {
    name: 'HelloWorld',
    props: {
      msg: 'Rollbar Example'
    },
    data: () => ({
      clicks: 0,
      json: '{ "not quite: json" }'
    }),
    methods: {
      parse: function () {
        JSON.parse(this.json)  // SyntaxError: JSON.parse: 
      }
    }            
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 查看滚动条中的错误

打开滚动条，查看这些错误在您帐户的项目页面中的样子。我们刚刚生成的错误应该被称为“syntax error:JSON . parse:expected”:'”

[![Screenshot of Rollbar Vue error item](img/c3e88ca5a6170ffc2f3dfa8154d35310.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KiFMR7jw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r93nby1v4dy2cbj5zp73.png)

通过单击项目获取更多详细信息。现在您可以看到一个回溯，显示了产生错误的确切的源代码文件、方法和行号。

[![Screenshot of Rollbar Vue error detail](img/bf9c7073460cecb946713d4f23e5bdda.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NDknQp03--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/suek2dx3ts22iaeoysvs.png)

现在你已经将滚动条集成到你的 Vue 应用中，任何发生的错误都将被捕获、分组并报告给滚动条。您将能够快速、轻松地看到发生了哪些错误、发生的频率以及完整的上下文。这将有助于您更快地排查原因并解决问题，从而减少受影响的客户数量。如果你还没有注册，请注册 Rollbar 的 14 天免费试用版。