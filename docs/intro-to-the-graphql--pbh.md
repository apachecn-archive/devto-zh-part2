# 图表简介

> 原文：<https://dev.to/sait/intro-to-the-graphql--pbh>

GraphQL 是 API 的查询语言，它帮助我们只获取所需的
资源，而不是过度获取，它不仅限于单一的
编程语言，是其他语言的替代。

## GraphQL 是一种类型特定的语言

通过使用模式定义
语言，可以很容易地创建 GraphQL 类型。

GraphQL 自带了一组标量类型

*   线
*   （同 Internationalorganizations）国际组织
*   浮动
*   布尔代数学体系的
*   身份证明

让我们在实践中看看它是如何工作的

我正在使用一个 Apollographl 包来创建一个 Graphql-server。

```
mkdir graphql-example
cd graphlql-example 
```

Enter fullscreen mode Exit fullscreen mode

我们需要初始化 Package.json 文件并安装依赖项

```
npm init -y
npm i --save graphql apollo-server@rc 
```

Enter fullscreen mode Exit fullscreen mode

在您喜欢的代码编辑器中打开 graphql-example 文件夹

[![](../Images/2a7ba19655bc23a23532551194e9abc7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QmEH5eU0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c9bzblvcwp5h49w078dr.png)

创建 server.js 文件

现在我们需要从阿波罗服务器请求。

```
const { gql, ApolloServer } = require('apollo-server'); 
```

Enter fullscreen mode Exit fullscreen mode

是时候创建一个类型定义了

在这个例子中，我展示了一个人的类型

```
const personType=gql`

type Person{
    name: String!
    age: Int!

}

type Query{
  getPerson: Person!
}
` 
```

Enter fullscreen mode Exit fullscreen mode

就像上面的代码一样，我们定义的人员类型必须包含姓名和年龄

查询意味着获取数据，就像如果我们调用一个 getPerson，我们的输出
应该像 Person 类型。

感叹号(！):这意味着字段不可为空

通过编写查询，GraphQL 不做任何事情，我们需要告诉 GraphQL 如何解析 getPerson 查询。

所以我们现在需要解决 getPerson 查询

```
const resolvers = {

    Query: {
        getPerson:()=>{
            return {
                name: 'James',
                age: 12
            }
        }

    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要将类型定义和解析器传递给 ApolloServer 构造函数。

```
const server = new ApolloServer({
    typeDefs: personType,
    resolvers
});

server.listen({
    port:5000
}).then(({ url }) => {
    console.log('Server is Up at' + url)
}); 
```

Enter fullscreen mode Exit fullscreen mode

server.js