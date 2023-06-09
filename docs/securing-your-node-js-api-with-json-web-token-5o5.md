# 用 JSON Web 令牌保护您的节点 js api

> 原文：<https://dev.to/medaymentn/securing-your-node-js-api-with-json-web-token-5o5>

# 简介

如今，REST(表述性状态转移)由于其简单的语法和灵活性，已经成为 web 架构中最常用的风格。REST 允许用户使他们的应用程序可扩展，事实上，来自不同前端平台的客户端可以以简单的方式使用 http 协议执行对服务器的请求，并交换可以用 JSON 或 XML 格式编码的数据。

现在，Restful 架构带来了所有的特性，但它仍然存在一些问题，特别是在安全性方面。

在众多用于保护 Restful api 的安全方法中，基于令牌的认证是一种

## 那么什么是基于令牌的认证呢？

让我们把它变得简单些:)

基于令牌的身份验证系统背后的一般概念很简单。

允许用户使用他们的用户名和密码来获取令牌，该令牌允许他们访问特定资源，而无需每次都使用他们的自然凭据。

一旦获得他们的令牌，用户就可以使用该令牌在一段时间内访问远程站点的服务器中的特定资源。

# 它是如何工作的？？

使用 jwt 的过程由 6 个步骤组成

1-使用凭据进行身份验证

2-一旦认证被批准，服务器生成一个包含 json web 令牌的随机字符串

3-将令牌返回给客户端

4-在客户端存储令牌

5-将令牌和每个 http 请求一起从客户端发送到服务器

6-服务器检查令牌是否有效，并授予对指定资源的访问权限

# 我们要建造什么？

在本文中，我们将使用 Node js 和 Express.js 构建一个 API，并将使用 postman 进行测试，所以让我们开始吧:)

首先让我们看看我们的项目结构

```
-/configurations
             ->/config.js 
-package.json

-index.js 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的项目已经准备就绪，让我们安装我们的软件包。

打开项目目录下的命令行，写下这个命令

```
npm install --save  express body-parser morgan jsonwebtoken 
```

Enter fullscreen mode Exit fullscreen mode

让我们解释一下我们已经安装的软件包

Express:著名的节点 js 框架

body-parser:允许我们从请求体中获取数据

morgan:在控制台中记录请求

jsonwebtoken:这个包允许我们生成 jwt 并构建我们的中间件来检查令牌是否有效。

现在让我们进入 config.js

# config.js

准确地说，这个文件是用来设置一些配置的，大多数用户需要这样做，以便更好地组织他们的项目。

他们可以为数据库或其他目的设置配置，在我们的例子中，我们将使用这个文件来设置我们的秘密，这个秘密将在创建我们的 jwt 时使用，因此文件应该看起来像这样

```
module.exports = {

    secret : "heymynameismohamedaymen"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们进入 index.js，这是我们应用程序中最重要的文件。

### index.js

```
const express = require('express'),
bodyParser = require('body-parser'),
morgan      = require('morgan'),
jwt    = require('jsonwebtoken'),
config = require('./configurations/config'),
app = express(); 

//set secret
app.set('Secret', config.secret);

// use morgan to log requests to the console
app.use(morgan('dev'));

// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: true }));

// parse application/json
app.use(bodyParser.json());

app.listen(3000,()=>{

 console.log('server is running on port 3000') 

});
app.get('/', function(req, res) {
    res.send('Hello world  app is running on http://localhost:3000/');
}); 
```

Enter fullscreen mode Exit fullscreen mode

要检查是否一切正常，请进入命令行并运行以下命令

```
 node index.js 
```

Enter fullscreen mode Exit fullscreen mode

在[上打开浏览器 http://localhost:3000/](http://localhost:3000/)

[![alt text](img/c62728124c60bf723ce5e3b3a6f326fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_g9uLT68--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.licdn.com/dms/image/C5612AQGv3Wzo_WtlHQ/article-inline_image-shrink_1000_1488/0%3Fe%3D2131315200%26v%3Dbeta%26t%3D3pyCup4k3Njba8FniWItQVEmeSYnswI3z3Pj4Ygq2c4)

一切看起来都很好！！

我们还可以看到，由于 morgan 包，请求被记录在我们的控制台中

[![alt text](img/1e803ce9cbf1ae4936138708e168a83a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VZ6t8wWa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.licdn.com/dms/image/C5612AQGUdedBZTTWlA/article-inline_image-shrink_1500_2232/0%3Fe%3D2131315200%26v%3Dbeta%26t%3DmduZcnE7Zoxh5YNUQq3vJuaq4wCP-VyjnEhBO7KVivQ)

# 设置认证系统

现在是我们应用程序最精彩的部分:)。

在这个应用程序中，我们实际上不会使用存储在数据库中的真实模型，但是我们将设置一个静态登录和密码来检查用户是否存在，因为我们在这篇文章中想要关注的是 JWT 的用法，所以您可以简单地更改我们将要编写的代码。

因此，在我们的 index.js 中，让我们创建认证路由

这里我们选择 aymen 作为用户名，123 作为密码

```
app.post('/authenticate',(req,res)=>{

    if(req.body.username==="aymen"){

        if(req.body.password===123){
             //if eveything is okey let's create our token 

        const payload = {

            check:  true

          };

          var token = jwt.sign(payload, app.get('Secret'), {
                expiresIn: 1440 // expires in 24 hours

          });

          res.json({
            message: 'authentication done ',
            token: token
          });

        }else{
            res.json({message:"please check your password !"})
        }

    }else{

        res.json({message:"user not found !"})

    }

}) 
```

Enter fullscreen mode Exit fullscreen mode

既然路线已经建好了，我们可以拿回我们的代币了..所以让我们和邮递员做一个测试

[![alt text](img/5cdfc6e31202fedfd1f02404b3c33a49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vk2Czb6B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.licdn.com/dms/image/C5612AQFbgfNzLSqWkA/article-inline_image-shrink_1000_1488/0%3Fe%3D2131315200%26v%3Dbeta%26t%3D-TirXQ5yeHxatmMOfrsOjByt8BHRfFElVwESdOEIZnM)

现在我们有了令牌，作为客户端，我们首先要以某种方式存储令牌，有许多工具可以做到这一点，例如，如果我们使用浏览器，我们可以使用 localstorage，或者如果我们使用 android 创建移动应用程序，我们可以使用 sharedpreferences

# 设置中间件

目前，我们有了令牌，可以向服务器发出 http 请求，但我们还必须构建中间件来处理每个 http 请求，搜索令牌并检查它是否有效。

但是在创建中间件之前，我们必须创建将被它保护的路由，所以在我们的 index.js 中，受保护的路由应该是这样的

```
const  ProtectedRoutes = express.Router(); 

app.use('/api', ProtectedRoutes); 
```

Enter fullscreen mode Exit fullscreen mode

现在/api 下的每个路由都将成为中间件保护的路由，为了访问使用/api 作为父路由的资源，我们必须为它提供正确的令牌。

为了将令牌连同一些数据一起发送给服务器，我们通常将令牌存储在每个请求的报头中，之后中间件将处理 http 请求并从报头中提取令牌。

因此，在我们的 index.js 中，让我们编写代码来完成这个任务

```
ProtectedRoutes.use((req, res, next) =>{

    // check header for the token
    var token = req.headers['access-token'];

    // decode token
    if (token) {

      // verifies secret and checks if the token is expired
      jwt.verify(token, app.get('Secret'), (err, decoded) =>{      
        if (err) {
          return res.json({ message: 'invalid token' });    
        } else {
          // if everything is good, save to request for use in other routes
          req.decoded = decoded;    
          next();
        }
      });

    } else {

      // if there is no token 

      res.send({ 

          message: 'No token provided.' 
      });

    }
  }); 
```

Enter fullscreen mode Exit fullscreen mode

为了检查我们的中间件是否工作，我们将创建另一个路由，在该路由中，我们将返回一组产品，我们将使用 ProtectedRoutes 在/api 下设置该路由，之后，我们将发出 GET 请求以取回数据。

首先让我们创建路线

```
ProtectedRoutes.get('/getAllProducts',(req,res)=>{
 let products = [
     {
         id: 1,
         name:"cheese"
     },
     {
        id: 2,
        name:"carottes"
    }
 ]

 res.json(products)

}) 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们尝试在不提供令牌的情况下获取产品列表，看看会发生什么

[![alt text](img/2416200eeec2167e4c0e380ee49f4883.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--01ReJbyd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.licdn.com/dms/image/C5612AQHMmR4nAf_Fww/article-inline_image-shrink_1500_2232/0%3Fe%3D2131315200%26v%3Dbeta%26t%3DRW_bRaZNK3sJqXatT5LId0QQXnUnofIrTXUt22VtXHs)

这一次，中间件没有返回数据，因为我们没有提供令牌，或者换句话说，api 没有识别我们，认为我们是想要获取一些信息的坏人。

因此，现在使用 postman，我们将把令牌放在请求的头部，让我们执行另一个请求

[![alt text](img/4b9a394fceacb369f24a86451be2a83f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iNr0NtzH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.licdn.com/dms/image/C5612AQEOQkmgPrQnUw/article-inline_image-shrink_1500_2232/0%3Fe%3D2131315200%26v%3Dbeta%26t%3D_h2HrI0jVKVOClU_KykA07bxNUt8B6FcvelNZXzaC_U)

现在一切看起来都很好，我们得到了数据；)).

# 结论

在这个例子中，我们很好地了解了 JWT 的用法以及它们对 Restful Api 的安全性的重要性，请注意，这不是 node js 应用程序安全性的唯一方法，但是有很多工具可能非常有用。

我们希望这个外观能让我们更好地理解如何创建令牌，如何保护路由，以及如何在 node js 应用程序中管理所有这些。

您可以在下面的链接中找到该项目:

([https://github.com/medaymenTN/JWTNodeJS](https://github.com/medaymenTN/JWTNodeJS)