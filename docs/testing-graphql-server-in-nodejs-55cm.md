# 测试 NodeJS 中的 GraphQL 服务器

> 原文：<https://dev.to/neshaz/testing-graphql-server-in-nodejs-55cm>

当 Kolosek 团队开始在我们的 NodeJS 项目中使用 GraphQL 时，我们在编写测试时遇到了困难:我们阅读了大量博客，寻找最佳方法。不幸的是，似乎没有，所以我们自己做了一个，在这里我们将分享我们测试 GraphQL 查询的方法。

本文将帮助您使用**[【柴】](http://chaijs.com/api/)****[supertest](https://github.com/visionmedia/supertest)**和 **[graphql](http://graphql.org/graphql-js/)** 来测试您的 GraphQL 服务器。

首先，我们将设置运行测试所需的一切:

```
npm i --save-dev mocha chai supertest graphql 
```

现在我们已经安装好了所有的东西，我们可以继续,**导入我们将在 *User.test.js* 文件中使用的所有依赖项**:

```
const chai = require('chai');

const expect = chai.expect;
const url = `http://localhost:3001/`;
const request = require('supertest')(url);

describe('GraphQL', () => {
    // Tests
}); 
```

*设置甚至比[设置 RSpec](https://kolosek.com/rails-rspec-setup/) 进行测试还要简单！*

在运行任何测试之前，我们必须向我们的 package . JSON“scripts”添加一个命令:

```
 "scripts": {
      "test": "mocha test/*.test.js"
   } 
```

这将告诉**摩卡**用*测试所有文件。在我们的测试文件夹中测试*扩展。要运行测试，只需键入:

```
npm test 
```

它会说 **0 通过**，因为我们还没有编写任何测试用例。

让我们定义一个 graphql 模式，我们将使用它来编写测试。例如，这里我们将在 *UserType.js* 文件中创建一个包含用户基本信息的用户类型:

```
const graphql = require('graphql');

export default new graphql.GraphQLObjectType({
name : 'UserType',
 fields : {
     id : {
         type : graphql.GraphQLInt
     },
     name : {
         type : graphql.GraphQLString
     },
     username:{
         type: graphql.GraphQLString
     },
     email : {
         type : graphql.GraphQLString
     }
}); 
```

我们正在组织我们的测试，就像我们组织 [RSpec 控制器测试](https://kolosek.com/rspec-controller-test/)一样！

让我们定义查询字段，该字段将向用户返回我们刚才定义的字段:

```
 const graphql = require('graphql');
 const UserType =  require('./UserType').default;

 export default {
  user: {
    description: 'Returns information about user/s',
    type: new graphql.GraphQLList(UserType),
    args: {
      id: { type: graphql.GraphQLInt },
    },
    resolve: async (_, { id }) => {
        if (id) return User.find({ id })
        return User.find() 
    }
  }, 
```

为了能够查询用户，我们必须定义 GraphQL 模式:

```
 const graphql = require('graphql');
 const UserQuery = require('./UserQuery');

 export default new graphql.GraphQLSchema({
     name: 'Query',
     fields: UserQuery
 }) 
```

在我们开始编写测试之前，要做的最后一件事是创建一个通用的 GraphQL 控制器，它将被每个查询触发，因此我们的客户端应用程序([我们使用的是 React](https://kolosek.com/react-first-steps/) )也有一个端点来发出请求，我们的测试也是如此。

```
const graphql = require('graphql');
const schema = require('./schema').default;

module.exports = {
  graphql: async (req, res) => {
    try {
      const result = await graphql(schema, req.body.query, req);
      if (result.errors) throw (result.errors);
      return res.ok(result);
    } catch (err) {
      return res.badRequest(err);
    }
  }, 
```

既然我们已经定义了我们的用户类型，为它定义了查询对象，并将其包含在我们的 GraphQLSchema 中，我们就可以开始**编写测试**了。

假设我们的数据库中有一些用户，剩下的唯一事情就是向我们的 *GraphQLController* 发送带有 *supertest* 的请求，并输出我们的查询结果:

```
const chai = require('chai');

const expect = chai.expect;
const url = `http://localhost:3001/`;
const request = require('supertest')(url);

describe('GraphQL', () => {
    it('Returns user with id = 10', (done) => {
        request.post('/graphql')
        .send({ query: '{ user(id: 10) { id name username email } }'})
        .expect(200)
        .end((err,res) => {
            // res will contain array with one user
            if (err) return done(err);
            res.body.user.should.have.property('id')
            res.body.user.should.have.property('name')
            res.body.user.should.have.property('username')
            res.body.user.should.have.property('email')
            done();
        })
    })

    it('Returns all users', (done) => {
        request.post('/graphql')
        .send({ query: '{ user { id name username email } }' })
        .expect(200)
        .end((err, res) => {
            // res will contain array of all users
            if (err) return done(err);
            // assume there are a 100 users in the database
            res.body.user.should.have.lengthOf(100);
        })  
    })
}); 
```

当然，柴图书馆提供了更多的选择。

到此，本文就告一段落了。

希望你喜欢它，并发现它的信息！

本文原载于 [Kolosek 博客](https://kolosek.com/testing-graphql-server/)。