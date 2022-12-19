# 在 VueJS 中测试 REST API

> 原文：<https://dev.to/oivoodoo/testing-rest-api-in-vuejs-5126>

我刚刚开始与`VueJS`一起为我在当前公司的分析工作构建仪表板原型。`VueJS`如此简单，以至于成为我最喜欢的构建 js 应用的工具。在`EmberJS`,每个人都在使用 mirage 库来存根 api 调用，并用所需的主体来响应。在`VueJS`中，我决定通过使用 [vue-resource-mock](https://github.com/noru/vue-resource-mock) 来归档相同的行为

为了便于理解如何使用这个令人敬畏的库，让我们举一些例子。

```
// main.js
...
import VueResource from 'vue-resource'
import VueResourceMock from 'vue-resource-mock'
import MockData from './mock/data.js'

Vue.use(VueResource)

if (process.env.NODE_ENV === 'development') {
  Vue.use(VueResourceMock, MockData)
}
... 
```

Enter fullscreen mode Exit fullscreen mode

```
// mock/data.js

const id = () => Math.floor(Math.random() * 100)

export default {

  ['GET */example'] (pathMatch, query, request) {
    let body = {
      collection1: [
        { id: id(), name: 'name1' },
        { id: id(), name: 'name2' },
      ]
    }

    return {
      body: body,
      status: 200
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果你的组件使用 http 资源获取数据，它应该和以前一样，不需要任何更改，只需调用 http 请求，你就会得到模拟数据。

```
...

      this.$http
        .get(`${process.env.API_HOST}/example`)
        .then(response => {
          let { collection1 } = response.body
          this.$store.commit('setCollection1', collection)
        })
... 
```

Enter fullscreen mode Exit fullscreen mode