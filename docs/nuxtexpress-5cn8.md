# 在 Nuxt+Express 的项目创建中，什么看起来比较好？

> 原文：<https://dev.to/dala00/nuxtexpress-5cn8>

我尝试了几种在 Nuxt.js 和 Express 中创建新项目的方法。 在 Nuxt.js 和 Express 中创建 APP 表示法非常有用，因为它在缺省情况下提供服务器端呈现( SSR )，并且处理过程也可以在服务器端和前端通用。

## 有什么样的制作方法？

项目本身也可以通过 vue-cli 创建。

### 基本的制作方法

如 Nuxt.js 文档中所述，有一个开始模板。

```
vue init nuxt-community/starter-template <project-name> 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以创建最少的 Nuxt.js 项目，然后将 Express 放入其中即可创建。 但是，可以更容易地创建包含 Express 的项目。

### 使用加入了 Express 的模板

如果您查看上面提到的 nuxt-community 中的其他存储库，我们还提供了其他几个模板。 加入 TypeScript 的 typescript-template 等。 其中还有 express-template，您可以使用它来创建项目。

```
vue init nuxt-community/express-template <project-name> 
```

Enter fullscreen mode Exit fullscreen mode

这样的话，可以更容易地创建 Nuxt.js+Express 的项目。

不过，我试了一下，发现即使更新了 Express 端的程序也不会进行热重新加载，每次都必须重新启动服务器。 因为有点麻烦，所以我也试着找了其他的项目制作方法。

### create-nuxt-appを使う

同样在 nuxt-community 的存储库中，有一个名为 create-nuxt-app 的存储库。 在为 Nuxt.js 创建 APP 应用程序时，这非常有用，因为您可以自行选择和创建配置。

[nuxt-community/Create-nuxt-App:几秒钟创建 Nuxt.js App。](https://github.com/nuxt-community/create-nuxt-app)

可以用以下任意一个指令来制作。 (我没有尝试 yarn )

```
npx create-nuxt-app <my-project> 
```

Enter fullscreen mode Exit fullscreen mode

```
yarn create nuxt-app <my-project> 
```

Enter fullscreen mode Exit fullscreen mode

可以选择以下构成。

*   服务器端框架( Express 等)
*   UI 框架( Bootstrap、Bulma 等)
*   要使用 axios 吗
*   使用 EsLint 或

另外，还出现了一看到 issue 就可以选择使用 TypeScript 的情况，如果被安装的话我会很高兴。 也期待今后。

现在选择 Express，可以创建运行 Express 的项目。 而且这个无论是修改 Nuxt.js 方面还是 Express 方面的程序都会很好地进行热重新加载。 (准确地说，是因为在 package.json 脚本的 nodemon 中指定了`--watch`。 如果应该监视的文件夹有多个的话，无论多少次都可以使用`--watch`)

为此，我尝试复制了 express-template 中的用户信息获取处理。 在 server/index.js 中添加以下处理，然后读取 users.js。 顺便把路由也以`/api`为基础。

```
const router = express.Router();
router.use(require("../routes/users"));
app.use("/api", router); 
```

Enter fullscreen mode Exit fullscreen mode

在 Vue 组件侧也同样读取。

```
 async asyncData() {
    let { data } = await axios.get("/api/users");
    return { users: data };
  } 
```

Enter fullscreen mode Exit fullscreen mode

显示也是一样。

```
<div v-for="user in users" :key="user.name">
  {{ user.name }}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

这次，我们暂且只做了用 Nuxt.js+Express 简单制作项目的触感部分。 还没有尝试制作内部版本等。 顺便说一下，我也想放 TypeScript，但是因为 Express 方面做得不好，所以暂时放弃了。

因为还在尝试其他 MySQL 连接和 Twitter 认证等 APP 所需的东西，所以如果顺利进行的东西增加了的话，我想再追加各种各样的报道。 (顺便说一下，DB 在 Sequelize 上顺利，我正在用 Passport 尝试 Twitter 认证。 因为认证有会话和 Nuxt.js 方面的 state 的对应，所以可能需要一点时间)