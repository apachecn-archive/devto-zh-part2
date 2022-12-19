# 在 React 应用程序中实现 JSON Web 令牌& Passport.js

> 原文：<https://dev.to/paigen11/implementing-json-web-tokens-passportjs-in-a-react-app-45cp>

[![Passport, JWT, Express, and React logos](img/e2cefab1e2f0018c71eaa38508bc5091.png "Passport, JWT, Express, and React logos")T2】](///static/ee5d14c2643892ad0a4beb4a46375dab/2cefc/passport-hero.png)

## 简介

在你说之前，我知道，我知道。已经有大量的 MERN 教程展示了如何在 Express 中使用 JSON Web Tokens (JWT，发音为“jot”)和 Passport.js。

但这里是每一个教程都没有提到的，而我将涵盖的是:*如何*和*为什么*使用 Passport 提供的各种身份验证功能(包括`passport-jwt`)，以及在我创建用户注册应用程序时连续几个小时困扰我的问题。

我不知道你有什么经验，但是我专业参与的大多数项目都已经得到了别人的授权。因此，除了提高我的 MERN 技能之外，我还将此视为一个学习更多关于安全性和身份验证以及不同实现方式的机会。

这篇文章将主要讲述如何在服务器端实现护照和 JWT，因为这是最神奇的地方，但是如果你愿意，你可以看到源文件并在这里下载完整的 MERN 项目。

一旦它被正确地连接到后端，前端就是纯粹的反应。

在我开始之前，让我给你一个 JWT 和 Passport.js 认证的快速运行。

* * *

## 什么是 JSON Web Token？

[![JWT logo](img/a3133a6de31c435bebbdb2f5c00d6a09.png "JWT logo")T2】](///static/5e49a7b09e89a1ae840c5546530d2d7c/ea7fb/jwt.png)

一个 **[JSON Web 令牌](https://jwt.io/)** ，显示的站点是:

> “…一种开放标准( [RFC 7519](https://tools.ietf.org/html/rfc7519) )，它定义了一种紧凑且独立的方式，以 JSON 对象的形式在各方之间安全地传输信息。”— [JWT.io](https://jwt.io/)

从本质上讲，jwt 是可以验证和信任的数字签名令牌，它们越来越受各方(如服务器和客户端)的安全和授权以及交换敏感信息的欢迎，同时验证令牌没有被篡改或解码。

我不会深入讨论令牌如何工作的细节，您可以从比我更有资格的来源阅读所有相关内容[这里是](https://jwt.io/introduction/)，但可以说，我选择使用 JWT 作为我的用户验证策略的一部分，以及 Passport.js 来使我的简单用户注册应用程序安全(因为我很好奇我是否能做到这一点，因为我没有太多实现授权的实践经验)。

至此，我的认证解决方案的下一部分是 Passport.js。

## 什么是 Passport.js &为什么要用？

[![Passport logo](img/fb5a21b4688686fbb1a2688300cdc80f.png "Passport logo")T2】](///static/278b555d574a51fcd97b9201fd9366cf/772e8/passport.png)

**[Passport.js](https://www.passportjs.org/)** 是:

> Node.js 的简单、不引人注目的身份验证— [Passport.js](https://www.passportjs.org/)

它与 Express.js 配合得非常好。Passport 是用于节点的身份验证中间件，它有一个目的，即以模块化的方式对请求进行身份验证，将所有其他功能留给应用程序本身，从而使代码更清晰、更易于维护，并提供清晰的关注点分离。

如果你在谷歌搜索栏中输入术语“JavaScript 认证中间件”，甚至仅仅是“JavaScript 认证”，Passport.js 会排在前 5 名的搜索结果中。这就是这个解决方案在 JavaScript 生态系统中无处不在的原因。

我提到过它拥有 500 多种身份认证策略吗？确实如此。您是否希望使用简单的用户名和密码、Github 凭证、脸书、oAuth 等登录。，可能有 Passport.js 策略。

所以选择 Passport 作为我的授权策略的一部分是一个简单的决定。

我想在网站上添加文档是很好的，尽管有些东西，如自定义回调，需要更仔细的阅读(对我来说，还有试错)才能正确设置。但是我超越了自己，我将讨论实现中的问题。

## 我如何在 Express.js 中实现它们(还有点 React)？

现在到了有趣的部分，如何在一个 Express/Node 应用程序中实现 Passport.js 和 JWT？老实说，它难倒了我好一阵子。但是在无数的教程、重读文档和求助于 Stack Overflow 之后，我找到了理解和满足的地方。

我应该说，我的这个应用程序的目标之一是使它模块化，使它非常简单地添加或删除功能，所以当你看到我的文件结构时，你会注意到 CRUD 功能(创建、读取、更新、删除)的所有路径都构建在单独的文件中，并且所有的 Passport 身份验证都在一个集中的文件中处理。这里看一下我的应用程序的 API 部分的文件结构。

### API 文件树

```
root/
├── api/ 
| ├── config 
| | ├── passport.js 
| | ├── jwtConfig.js
| ├── server.js 
| ├── sequelize.js 
| ├── package.json
| ├── models/ 
| | ├── user.js
| ├── routes/ 
| | ├── deleteUser.js 
| | ├── findUsers.js 
| | ├── loginUser.js 
| | ├── registerUser.js
| | ├── updateUser.js
| ├── node-modules/ 
```

Enter fullscreen mode Exit fullscreen mode

### `package.json`文件

这里是`package.json`和它的依赖项，所以你可以看到我到底在用什么。

**[`package.json`](https://github.com/paigen11/mysql-registration-passport/blob/master/api/package.json)**T5】

```
{
  "name": "mysqlregistration-api",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "nodemon server.js --exec babel-node --presets es2015"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/paigen11/mysqlRegistration.git"
  },
  "author": "Paige Niedringhaus",
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/paigen11/mysqlRegistration/issues"
  },
  "homepage": "https://github.com/paigen11/mysqlRegistration#readme",
  "dependencies": {
    "babel-cli": "^6.26.0",
    "babel-preset-es2015": "^6.24.1",
    "babel-preset-stage-0": "^6.24.1",
    "bcrypt": "^3.0.2",
    "body-parser": "^1.18.3",
    "cors": "^2.8.4",
    "dotenv": "^6.1.0",
    "express": "^4.16.3",
    "helmet": "^3.20.1",
    "jsonwebtoken": "^8.3.0",
    "morgan": "^1.9.0",
    "mysql2": "^1.5.3",
    "nodemailer": "^4.6.8",
    "nodemon": "^1.18.3",
    "passport": "^0.4.0",
    "passport-jwt": "^4.0.0",
    "passport-local": "^1.0.0",
    "sequelize": "^5.3.0",
    "sequelize-cli": "^4.0.0",
    "swagger-jsdoc": "^3.2.9",
    "swagger-ui-express": "^4.0.2"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

依赖项包括一些额外的东西，比如`babel`，这样我可以在我的 Node.js 应用程序中使用 ES6 语法，`bcrypt`用于密码哈希，`sequelize`作为我的 MySQL ORM，但是你需要关注的是`jsonwebtoken`、`passport`、`passport-local`和`passport-jwt`。这些是这个博客的必需品。

### `server.js`文件

我将首先从`server.js`文件开始，因为它需要的解释最少。这个文件纯粹是为了启动服务器，初始化 Passport 在应用程序中的使用，以及设置路由和解析来自客户端的请求。

**[`server.js`](https://github.com/paigen11/mysql-registration-passport/blob/master/api/server.js)**T5】

```
import express from 'express';
import Cors from 'cors';
import bodyParser from 'body-parser';
import logger from 'morgan';
import passport from 'passport';
import helmet from 'helmet';

const app = express();

const API_PORT = process.env.API_PORT || 3000;

require('./config/passport');

app.use(Cors());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());
app.use(logger('dev'));
app.use(helmet());
app.use(passport.initialize());

require('./routes/loginUser')(app);
require('./routes/registerUser')(app);
require('./routes/findUsers')(app);
require('./routes/deleteUser')(app);
require('./routes/updateUser')(app);

// eslint-disable-next-line no-console
app.listen(API_PORT, () => console.log(`Listening on port ${API_PORT}`));

module.exports = app; 
```

Enter fullscreen mode Exit fullscreen mode

`imports`和`consts`中有一点 ES6，但你要重点关注的是`passport.initialize()`和`routes`。

很简单，对吧？太好了。继续前进。

你可能也注意到了`require('./config/passport');`，这是我要讲的下一步。JWT 和护照的配置，在名为`config/`的文件夹里面。

### `jwtConfig.js`文件

JWT 配置非常简单，它是 JWT 编码和解码令牌所需的秘密。通常，这将作为一个环境变量存储在一个没有签入 Github 的文件中，但是为了展示这是如何工作的，我将它设置在这里。

**[`jwtConfig.js`](https://github.com/paigen11/mysql-registration-passport/blob/master/api/config/jwtConfig.js)**T5】

```
module.exports = {
  secret: 'jwt-secret',
}; 
```

Enter fullscreen mode Exit fullscreen mode

### `passport.js`文件

**[`passport.js`](https://github.com/paigen11/mysql-registration-passport/blob/master/api/config/passport.js)**T5】

```
import bcrypt from 'bcrypt';
import jwtSecret from './jwtConfig';

const BCRYPT_SALT_ROUNDS = 12;

const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;
const JWTstrategy = require('passport-jwt').Strategy;
const ExtractJWT = require('passport-jwt').ExtractJwt;
const User = require('../sequelize');

passport.use(
  'register',
  new LocalStrategy(
    {
      usernameField: 'username',
      passwordField: 'password',
      passReqToCallback: true,
      session: false,
    },
    (req, username, password, done) => {
      try {
        User.findOne({
          where: {
            [Op.or]: [
              {
                username,
              },
              { email: req.body.email },
            ],
          },
        }).then(user => {
          if (user != null) {
            console.log('username or email already taken');
            return done(null, false, {
              message: 'username or email already taken',
            });
          }
          bcrypt.hash(password, BCRYPT_SALT_ROUNDS).then(hashedPassword => {
            User.create({
              username,
              password: hashedPassword,
              email: req.body.email,
            }).then(user => {
              console.log('user created');
              return done(null, user);
            });
          });
        });
      } catch (err) {
        return done(err);
      }
    },
  ),
);

passport.use(
  'login',
  new LocalStrategy(
    {
      usernameField: 'username',
      passwordField: 'password',
      session: false,
    },
    (username, password, done) => {
      try {
        User.findOne({
          where: {
            username,
          },
        }).then(user => {
          if (user === null) {
            return done(null, false, { message: 'bad username' });
          }
          bcrypt.compare(password, user.password).then(response => {
            if (response !== true) {
              console.log('passwords do not match');
              return done(null, false, { message: 'passwords do not match' });
            }
            console.log('user found & authenticated');
            return done(null, user);
          });
        });
      } catch (err) {
        done(err);
      }
    },
  ),
);

const opts = {
  jwtFromRequest: ExtractJWT.fromAuthHeaderWithScheme('JWT'),
  secretOrKey: jwtSecret.secret,
};

passport.use(
  'jwt',
  new JWTstrategy(opts, (jwt_payload, done) => {
    try {
      User.findOne({
        where: {
          id: jwt_payload.id,
        },
      }).then(user => {
        if (user) {
          console.log('user found in db in passport');
          done(null, user);
        } else {
          console.log('user not found in db');
          done(null, false);
        }
      });
    } catch (err) {
      done(err);
    }
  }),
); 
```

Enter fullscreen mode Exit fullscreen mode

是的，这一切都是`passport.js`文件的一部分。

我知道这需要理解很多代码。如果你想只看到护照的代码和护照认证的路线，我在这里用代码创建了几个 gist。

我将介绍这里发生的两种类型的 Passport 实现:**[`register`和`login`方法的 passport-local](https://www.passportjs.org/packages/passport-local/)** 和 **[方法的 passport-jwt](https://www.passportjs.org/packages/passport-jwt/)** 。

`passport-local`使用用户名和密码，`passport-jwt`使用 JWT 有效载荷来验证用户是合法的。

正如我前面说过的，只使用 Express.js 的文档是相当好的，但是技巧在于将它与 MySQL 数据库和从前端 React 客户端传递的信息结合起来。

一旦向`passport-local`策略传递了用户名和密码(在回调服务器之前，我会验证这两个输入至少都在客户端填写了)，我在后端做的第一个检查是用 [Sequelize](https://sequelize.org/) ，我的 SQL ORM(就像 Mongoose for MongoDB)，来确定用户名是否存在于数据库中。如果它返回`null`，认证失败(数据库中没有匹配的用户)，通常情况下，一个`401 Unauthorized` HTTP 状态会从服务器返回到客户端，没有进一步的信息。

### Passport.js 陷阱#1:错误处理中的信息

对我来说，这种类型的错误处理是 Passport 最令人沮丧的地方。缺乏信息让用户(或我自己)知道超出`401 Unauthorized`的实际错误是什么。

但是有一个更好的解决方案，需要一点额外的工作，用**定制回调**。我将很快更详细地介绍回调，但是现在，您可以看到是否有某种错误，而不是返回一个`return done(null, user);`，Passport 可以传递回`return done(null, false, { message: 'bad username or passwords don't match' })`；。

然后这个消息可以从服务器传回到客户端，实际上告诉用户问题是什么(这是我对这个应用程序的期望)。如果您更喜欢告诉用户他们的身份验证失败了，但不透露原因，这也很酷。您的选择，但是我想展示错误消息是如何实现的。

`passport-local`策略的其余部分是不言而喻的，在`register`上，用户的密码用加密包 **[`bcrypt`](https://github.com/kelektiv/node.bcrypt.js)** 散列并加盐，然后当`login`方法被调用时，它散列新输入的密码并用 bcrypt 的`compare`函数检查密码，然后返回肯定或否定的验证结果。

现在，我要谈谈名为`jwt`的`passport-jwt`策略。这是在应用程序中受保护的路由上调用的认证:`findUsers`、`updateUser`、`deleteUser`。

对于 jwt 策略，每次调用服务器时，JWT 都从客户端传递回来(我在一个带有关键字:`JWT`的授权头中传递了我的请求)，提取出该请求，然后使用秘密(存储在另一个文件中，但实际上应该是只有系统知道的环境变量)进行解码。

一旦 JWT 有效载荷被解密，ID(对我来说就是用户名)就可以像`passport-local`策略一样在数据库中被搜索，如果找到用户，就用`done(null, user);`返回，如果找不到，就用`done(null, false);`返回(这几乎不会发生，因为 JWT 以加密形式包含用户名，所以除非用户名被篡改或数据库被篡改，否则它应该能够找到用户)。

### Passport.js Gotcha #2:护照-当地想要返回，护照-JWT 不想

这让我想到了我的第二个问题，这个问题困扰了我很长时间；并非所有的 passport 策略都需要相同的分辨率。

如果你仔细观察，你会发现`passport-local`策略都有`return done(null, user);`，但是`passport-jwt`策略有`done (null, user);`。看出区别了吗？这很小，但是有(或者去掉那个`return`)就是用户数据从中间件传回服务器与否的区别。

它使我的进度停止了好几个小时，直到 Stack Overflow 的好心帮助我解决了这个问题(这是我第一次不得不这样要求一个我已经找不到的答案，但这是非常值得的)。所以要注意什么时候该`return`或者不该`return`。

好了，我已经介绍了我的程序中用于中间件认证的两种 Passport 策略和三种方法，现在继续介绍服务器上的认证。

### `register.js`文件

下一步是如何在各种路由中实现这些新创造的方法。这里是`registerUser`路线。

**[`registerUser.js`](https://github.com/paigen11/mysql-registration-passport/blob/master/api/routes/registerUser.js)**T5】

```
import passport from 'passport';
import User from '../sequelize';

module.exports = app => {
  app.post('/registerUser', (req, res, next) => {
    passport.authenticate('register', (err, user, info) => {
      if (err) {
        console.error(err);
      }
      if (info !== undefined) {
        console.error(info.message);
        res.status(403).send(info.message);
      } else {
        req.logIn(user, error => {
          const data = {
            first_name: req.body.first_name,
            last_name: req.body.last_name,
            email: req.body.email,
            username: user.username,
          };
          User.findOne({
            where: {
              username: data.username,
            },
          }).then(user => {
            user
              .update({
                first_name: data.first_name,
                last_name: data.last_name,
                email: data.email,
              })
              .then(() => {
                console.log('user created in db');
                res.status(200).send({ message: 'user created' });
              });
          });
        });
      }
    })(req, res, next);
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，Passport 的这个实现看起来与大多数例子有些不同，这是因为我使用的是定制回调版本的 Passport，它需要一个闭包调用。我使用它是为了当 Passport 的认证失败时返回的错误消息可以从服务器发送到客户端，而不是晦涩的`401 Unauthorized`。

因为这是作为回调实现的(这就是为什么你在这些脚本中看到`(req, res, next)`不是一次，而是两次；它允许我从 Passport 中间件访问`(err, user, info)`。

我感兴趣的是信息——那是被发回的消息，所以如果信息是除了`null`之外的任何东西，那就意味着认证失败了，然后我可以将消息发送给客户端，让他们知道原因。

### Passport.js 问题 3:在你的自定义回调中不要忘记`req.logIn()`

现在，我到了第三个 Passport gotcha，为了使闭包和定制回调与`passport-local`一起工作，如果从中间件成功返回的`user`数据被处理之前，必须调用小方法 **[`req.logIn()`](https://www.passportjs.org/concepts/authentication/login/)** 。

> 这在 [Passport 文档](https://www.passportjs.org/concepts/authentication/login/)中有记录，但是它的重要性并没有像我希望的那样被强调(在最初几次试图让自定义回调工作时，我错过了它)，这就是为什么我现在强调它。

这必须发生，一旦完成，我就能够从客户端获取注册用户的额外输入，找到在调用`passport-local` `register`期间在数据库中创建的相同用户，并用额外信息更新用户文件。我也可以将这些额外的数据传递给中间件，但是我希望 Passport 只处理认证，而不处理用户创建。模块化，记住。

此外，如果将身份验证拆分到一个单独的服务中，该服务具有一个单独的数据库，其中仅包含加密的用户名和密码，这样会更容易做到这一点，然后使用用户名或 ID 在该注册服务中查找并更新相应的用户记录。

一旦所有这些都被成功处理，一个`200` HTTP 状态和成功消息被发送回客户端。

### `loginUser.js`文件

现在看`loginUser`路线应该更有意义。

**[`loginUser.js`](https://github.com/paigen11/mysql-registration-passport/blob/master/api/routes/loginUser.js)**T5】

```
import jwt from 'jsonwebtoken';
import passport from 'passport';
import jwtSecret from '../config/jwtConfig';
import User from '../sequelize';

module.exports = app => {
  app.post('/loginUser', (req, res, next) => {
    passport.authenticate('login', (err, users, info) => {
      if (err) {
        console.error(`error ${err}`);
      }
      if (info !== undefined) {
        console.error(info.message);
        if (info.message === 'bad username') {
          res.status(401).send(info.message);
        } else {
          res.status(403).send(info.message);
        }
      } else {
        req.logIn(users, () => {
          User.findOne({
            where: {
              username: req.body.username,
            },
          }).then(user => {
            const token = jwt.sign({ id: user.id }, jwtSecret.secret, {
              expiresIn: 60 * 60,
            });
            res.status(200).send({
              auth: true,
              token,
              message: 'user found & logged in',
            });
          });
        });
      }
    })(req, res, next);
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

使用了相同风格的自定义回调和闭包，最大的不同在于，一旦用户被成功验证并定位到数据库中，就使用`jwt.sign();`函数生成 JWT 令牌，该函数将用户名设置为在 JWT 中传递的 ID，并使用我之前设置的秘密进行加密。

同样，如果所有这些工作都成功，则发送一个`200` HTTP 状态，对于客户端，一个名为`auth`的布尔 I 设置为 true，新生成的令牌和一个简短的登录成功消息。

### `findUsers.js`文件

最后但同样重要的是，这是`findUsers`路线。我对`updateUser`和`deleteUser`路由使用了相同的`passport-jwt`认证，所以我只展示这一个作为例子。

**[`findUsers.js`](https://github.com/paigen11/mysql-registration-passport/blob/master/api/routes/findUsers.js)**T5】

```
import passport from 'passport';
import User from '../sequelize';

module.exports = (app) => {
  app.get('/findUser', (req, res, next) => {
    passport.authenticate('jwt', { session: false }, (err, user, info) => {
      if (err) {
        console.log(err);
      }
      if (info !== undefined) {
        console.log(info.message);
        res.status(401).send(info.message);
      } else if (user.username === req.query.username) {
        User.findOne({
          where: {
            username: req.query.username,
          },
        }).then((userInfo) => {
          if (userInfo != null) {
            console.log('user found in db from findUsers');
            res.status(200).send({
              auth: true,
              first_name: userInfo.first_name,
              last_name: userInfo.last_name,
              email: userInfo.email,
              username: userInfo.username,
              password: userInfo.password,
              message: 'user found in db',
            });
          } else {
            console.error('no user exists in db with that username');
            res.status(401).send('no user exists in db with that username');
          }
        });
      } else {
        console.error('jwt id and username do not match');
        res.status(403).send('username and jwt token do not match');
      }
    })(req, res, next);
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

我对所有通过 Passport 验证的路由都使用了自定义回调(我喜欢良好、清晰的错误处理)，所以这种代码风格现在应该看起来很常规了。

这一次，当调用`passport.authenticate()`时，我实现了在`passport.js`文件中定义的 JWT 策略。同样的`(err, user, info)`从中间件返回，但是这次没有来自`req.logIn()`的调用。

相反，我只是返回在身份验证期间找到的`user`对象，并将所有必需的字段连同`auth`布尔值和成功消息一起传递给客户端。JWT 令牌仍然存储在客户端的本地存储中，因此不需要重新生成或再次传递给客户端。这让我想到了我的最后一张护照。

### Passport.js 问题 4:正确传递授权头

这并不完全是护照特有的问题，但这是另一件让我犯错误的事情。

正如我所说的，我从一堆其他教程和文档中拼凑了我对 JWT 和护照的理解(现在更完整了)，其中一个教程把我引入了歧途。除了真正的授权头之外，它还让我将 JWT 传递回客户端，这是我选择让我的`passport-jwt`策略从客户端请求中提取 JWT 有效载荷的方式。

出于这个原因，我想简单介绍一下前端代码的一部分，在那里我以正确的格式传回令牌，这样您就不必像我一样浪费时间调试了。

**[`Profile.js`](https://github.com/paigen11/mysql-registration-passport/blob/8d94f0289ec97d86683049b1dc06efa37f95f474/client/src/containers/Profile.js)**T5】

```
 async componentDidMount() {
    const accessString = localStorage.getItem('JWT');
    const {
      match: {
        params: { username },
      },
    } = this.props;

    if (accessString == null) {
      this.setState({
        isLoading: false,
        error: true,
      });
    } else {
      try {
        const response = await axios.get('http://localhost:3003/findUser', {
          params: {
            username,
          },
          headers: { Authorization: `JWT ${accessString}` },
        });
        this.setState({
          first_name: response.data.first_name,
          last_name: response.data.last_name,
          email: response.data.email,
          username: response.data.username,
          password: response.data.password,
          isLoading: false,
          error: false,
        });
      } catch (error) {
        console.error(error.response.data);
        this.setState({
          error: true,
        });
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

我使用流行的基于 promise 的 HTTP 客户端 **[Axios](https://www.npmjs.com/package/axios)** 来调用我的服务器，但是你真正需要注意的是`headers`部分。

在调用服务器之前，我使用`localStorage.getItem('JWT')`(当我从服务器端的`loginUser()`路由转发令牌时，我用 JWT 值设置的密钥)从本地存储中提取 JWT，然后我使用`headers: { Authorization:` JWT ${accessString} `}`和一点 ES6 字符串插值来设置它。这样，如果我的授权头有不止一个值需要解析，那么`passport-jwt`仍然可以通过找到与“JWT”相关的字符串来轻松提取正确的 JWT 有效载荷信息。

就是这样，我只是想覆盖它，因为它阻止了我一段时间，只有通过与 **[失眠](https://insomnia.rest/)** 的 API 测试，我才能确认我的 JWT 令牌实际上是工作的，并发现我从客户端错误地传递授权头。

* * *

## 结论

我知道我写了很多(并且提供了一堆代码片段和 gists)，但是认证不是一个简单的概念。也不可能让一个应用程序变得如此安全，没有人能够破解它。但是如果你像 JWT 和 Passport.js 认证中间件一样采取预防措施，你的 Node.js 应用应该有更好的机会抵御怀有恶意的人。

请务必留意我在 Passport 中强调的陷阱，这样您就可以访问更安全的网站。

过几周再来看看——我会写更多关于 JavaScript、React、IoT 或其他与 web 开发相关的东西。

感谢阅读，我希望这证明是有帮助的，并让您更好地理解实现 Passport 身份验证和使用 JSON web 令牌。

如果你想确保你不会错过我写的一篇文章，在这里注册我的时事通讯:[https://paigeniedringhaus.substack.com](https://paigeniedringhaus.substack.com)

* * *

## 进一步引用&资源

*   [Passport.js](http://www.passportjs.org/) 网站
*   [JSON 网络令牌](https://jwt.io/)网站
*   [MERN 护照 App&JWT 回购](https://github.com/paigen11/mysql-registration-passport)
*   [带路线的护照实施要点](https://gist.github.com/paigen11/c72c8c20da9cd440f45025a1b05e5e58)
*   [Axios](https://www.npmjs.com/package/axios) HTTP 库