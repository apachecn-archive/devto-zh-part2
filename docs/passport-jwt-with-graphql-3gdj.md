# 带 GraphQL 的 JWT 护照(阿波罗服务器 2)

> 原文：<https://dev.to/hasusozam/passport-jwt-with-graphql-3gdj>

我在研究和阅读 GraphQL 中的认证，人们告诉我(来自 Docker 和脸书的开发者)GraphQL 不需要知道任何关于认证的事情，事实上你的模式和解析器应该简单明了，认证必须在 GraphQL 之外完成，为此我在 GraphQL 中做了这个简单的 Passport JWT 认证来展示我对此的理解。

```
import express from 'express'
import { ApolloServer } from 'apollo-server-express'

// ...
import passport from 'passport'
import passportJWT from 'passport-jwt'
import jwt from 'jsonwebtoken'
// ...

import schema from './schemas'

const { GRAPHQL_PORT, JWT_SECRET } = process.env

// ...
const users = [
  {
    id: 1,
    name: 'John',
    email: 'john@mail.com',
    password: 'john123'
  }
]

// generate a jwt token for testing purposes
console.log(jwt.sign(users[0], JWT_SECRET))

// ...

const { Strategy, ExtractJwt } = passportJWT

const params = {
  secretOrKey: JWT_SECRET,
  jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken()
}

const strategy = new Strategy(params, (payload, done) => {
  const user = users.find(user => user.id === payload.id) || null

  return done(null, user)
})

passport.use(strategy)

// ...

const app = express()

// ...

passport.initialize()

app.use('/graphql', (req, res, next) => {
  passport.authenticate('jwt', { session: false }, (err, user, info) => {
    if (user) {
      req.user = user
    }

    next()
  })(req, res, next)
})

// ...

const server = new ApolloServer({
  schema,
  context: ({ req }) => ({
    user: req.user
  })
})

server.applyMiddleware({
  app
})

app.listen(
  {
    port: GRAPHQL_PORT
  },
  () => console.log(`The GraphQL server is running on port ${GRAPHQL_PORT}`)
) 
```

Enter fullscreen mode Exit fullscreen mode

现在解析器将对用户进行身份验证，但是解析器并不负责进行身份验证，所以最好是创建一个模式指令来检查身份验证，或者甚至将当前用户传递给后端的某种服务来检查，而不是在解析器中使用`if (user) {...`。

你对代码有什么想法，还有改进的空间吗？