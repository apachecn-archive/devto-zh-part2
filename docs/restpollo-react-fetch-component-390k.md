# Restpollo - React 获取组件

> 原文：<https://dev.to/irideveloper/restpollo-react-fetch-component-390k>

## T2】

> 声明:Restpollo 不是官方的阿波罗项目。他们在 React Apollo 上做得非常好，这启发我创建了一个简化版本，使用 React 组件和函数作为子模式与 REST API 进行通信。

我不掩饰我是 React ( [链接](https://reactjs.org/))、GraphQL ( [链接](https://graphql.org/))、Apollo ( [链接](https://www.apollographql.com/))和 Apollo 的 React 组件([链接](https://www.apollographql.com/docs/react/))的**】T1】的超级粉丝。当 Apollo React 推出 2.1 版本时，他们引入了查询、变异和订阅 GraphQL 后端的组件…这让我大吃一惊！
这里有一个例子(摘自 Apollo React 文档- [链接](https://www.apollographql.com/docs/react/react-apollo-migration.html) ):** 

```
<Query query={GET_DOGS}>
  {({ loading, error, data }) => {
    if (error) return <Error />
    if (loading || !data) return <Fetching />

    return <DogList dogs={data.dogs} />
  }}
</Query> 
```

Enter fullscreen mode Exit fullscreen mode

让我们详细检查一下上面的代码。

### **查询**

是一个 React Apollo 组件，它接受一个 GraphQL 查询作为道具，然后开始发挥它的魔力。
它转到 GraphQL 后端并获取我们请求的数据(在我们的示例中，在 GET_DOGS 中定义)，它还处理一些常见的状态:
**加载** -请求已发送，但我们尚未收到响应
**错误** -尝试检索数据时出错
**数据** -获取数据成功！

### **最酷的部分**

关于`<Query>`(和其他 Apollo React 组件)真正酷的部分是它如何处理呈现具有子模式功能的 UI([链接](https://medium.com/merrickchristensen/function-as-child-components-5f3920a9ace9))。
这意味着您可以获得所有美味的 Apollo React 功能，而无需假设您想要如何呈现您的数据！厉害！

* * *

# **安息遇上阿波罗**

因为我从 Apollo 的 React 组件中获得了如此多的乐趣，所以我想如果像 Apollo React 这样的东西能够与 REST API 进行通信该有多好。
在一次**的研究会议之后(谷歌了“apollo rest”并发现第一个结果不是我想要的)，我决定为 REST API 构建自己的 Apolloesque React 组件！**

 **> 是衍生的吗？是的。我会被阿波罗起诉吗？是的，但是我希望他们认为我很可爱，不要采取法律行动<*祈祷*T2】

### **盒子里是什么？**

我们故事的主角是`<Fetch>`,它接受两个道具“url”和“fetchOptions ”,它的魔力将允许你像孩子一样使用函数编写自己的渲染逻辑，就像阿波罗反应一样。
我们英雄的助手是`<Get>`、`<Post>`、`<Put>`和`<Delete>`，它们只不过是`<Fetch>`之上的语法糖，为常见的 REST 动作定义了默认值。
现在是好东西！
[示例站点链接](https://irideveloper.github.io/restpollo/) -在这里您可以看到组件如何工作，以及一些简单的布局和样式
[Github 链接](https://github.com/IRIdeveloper/restpollo) -如果您想查看代码，这是为您准备的链接< *手指点表情符号* >
[npm 链接](https://www.npmjs.com/package/react-restpollo) -来吧，您知道这是什么

* * *

# **【做我的朋友节】**

如果你喜欢这篇文章，想和我交朋友，你知道，网上聊天或喝杯啤酒或进行一次改变生活的冒险，这将使我们的余生联系在一起，你可以在这里找到我:
[Github](https://github.com/IRIdeveloper)
[Linkedin](https://www.linkedin.com/in/gvozderacbojan/)
[Medium](https://medium.com/@bojangbusiness)
[Twitter](https://twitter.com/GvozderacBojan)**