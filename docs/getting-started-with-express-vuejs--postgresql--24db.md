# 快速入门，VueJS 和 PostgreSQL

> 原文：<https://dev.to/jesalg/getting-started-with-express-vuejs--postgresql--24db>

[![PEVN](img/fc7623d5b41a0d10e975b0fd03952ee8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5WS1BmJL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jes.al/public/wp-content/uploads/pevn.png)

我们都听说过 MEAN(**M**ongoDB**E**xpress**A**ngular**N**odeJS)堆栈，或者最近听说过 MERN(**M**ongoDB**E**xpress**R**eact 和 **N** odeJS)堆栈。

有很多初学者工具包利用这些堆栈，虽然我在寻找类似的东西，但有一些变化。我想用 PostgresSQL 替换掉 MongoDB，因为它是一个几乎可以做任何事情的主力，而用 VueJS 替换掉 React，因为我发现 Vue 更容易接近，对初学者更友好。

我在那里没有找到任何类似的东西，所以我最终自己创建了一个。我知道，我们称之为 PEVN(**P**ostgreSQL**E**xpress**V**ueJS**N**odeJS)堆栈...0 为创意！

我花了几个小时让一切如我所愿地运转起来。我记录了这个过程，为那些想做同样事情的人省去麻烦，你可以在下面找到。

**TL；https://github.com/jesalg/penv-starter 博士**-

**节点 j**

在我们开始之前，让我们确保已经安装了 NodeJS。我发现最简单的方法是通过 nvm。Rails 开发人员会发现这与 rvm 非常相似。要安装，请运行以下命令，这将安装 nvm 和最新版本的 NodeJS:

```
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.32.1/install.sh | bash
$ source ~/.bash_profile
$ nvm install node
$ nvm use node 
```

Enter fullscreen mode Exit fullscreen mode

**快递**

安装 Express 最简单的方法是使用生成器:

```
$ npm install express-generator -g
$ express pevn-starter
$ cd pevn-starter && npm install 
```

Enter fullscreen mode Exit fullscreen mode

**VueJS**

现在我们有了一个基本的应用程序，让我们开始有趣的部分。我们将把每个 Express 视图视为它自己的 VueJS 应用程序(MVVM 模式),该应用程序将从 DOM 获取它需要的数据和/或向服务器发出 AJAX 请求。

因此，对于这个例子，假设我们有`views/index.jade`，我们将希望把它的相关 VueJS 应用程序和样式放在`client/css/index.css`、`client/js/index.js`和`/client/js/Index.vue`中，这样当 Jade 视图被渲染时，它将运行 Index Vue 应用程序。

所以我们必须在`views/index.jade`中告诉我们的视图来加载我们打包的资产文件:

```
extends layout

block content
  #index(data-visitors-json="#{JSON.stringify(visitors)}")
  script(src="#{webpack_asset('index', 'js')}")
  link(rel='stylesheet', href="#{webpack_asset('index', 'css')}") 
```

Enter fullscreen mode Exit fullscreen mode

我们的`client/js/index.js`将引导我们的 Index Vue 应用:

```
import Vue from 'vue'
import Index from './Index.vue'

new Vue({
  el: '#index',
  data: {
    visitors: []
  },
  render (createElement) {
    return createElement(Index)
  },
  beforeMount() {
    this.visitors = JSON.parse(this.$el.dataset.visitorsJson) //Grab this data from the DOM
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们的 Vue 应用程序位于`client/js/Index.vue` :

```
<template>
    <div>
        <h1>Hello World</h1>
        <p>Welcome to PostgreSQL, Express, VueJS, NodeJS starter</p>
        <p>Here are the last 10 visitors:</p>
        <table>
          <thead>
            <th>ID</th>
            <th>IP</th>
            <th>User Agent</th>
          </thead>

          <tr v-for="(visitor, index) in visitors" :key="index">
              <td>{{ visitor.id }}</td>
              <td>{{ visitor.ip }}</td>
              <td>{{ visitor.user_agent }}</td>
          </tr>
        </table>
    </div>
</template>

<script>
export default {
  data() {
    return {
      visitors: []
    }
  },
  methods: {

  },
  created() {
    this.visitors = this.$parent.visitors; //Grab this data from the parent
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

现在还不要担心显示访问者列表的逻辑。我们一会儿就会谈到这一点。

**网络包**

为了为我们的视图创建一个打包的 index.js 资产文件，我们需要安装 Webpack、VueJS 及其相关的依赖项:

```
$ npm install webpack extract-text-webpack-plugin assets-webpack-plugin babel-core babel-loader babel-preset-es2015 css-loader file-loader style-loader url-loader vue-template-compiler --save-dev
$ npm install vue express-webpack-assets webpack-dev-middleware webpack-hot-middleware 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们在项目的根目录下创建 webpack.config.js，并将以下内容粘贴到其中:

```
var path = require('path')
var webpack = require('webpack')
var ExtractTextPlugin = require("extract-text-webpack-plugin");
var SaveHashes = require('assets-webpack-plugin');
var isProd = (process.env.NODE_ENV === 'production');

var config = {
  entry: {
    index: [
      path.join(__dirname, 'client/js/index.js'),
      path.join(__dirname, 'client/css/index.css')
    ],
  },
  output: {
    path: path.join(__dirname, 'public/dist/'),
    publicPath: '/dist/',
    filename: '[name].[hash].js'
  },
  resolve: {
    extensions: ['.js', '.vue'],
    alias: {
      vue: isProd ? 'vue/dist/vue.min.js' : 'vue/dist/vue.js',
    }
  },
  module: {
    rules: [{
        test: /\.vue$/,
        exclude: /node_modules/,
        use: [{
          loader: 'vue-loader'
        }]
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: [{
          loader: 'babel-loader',
          options: {
            presets: ['es2015']
          }
        }]
      },
      {
        test: /\.svg/,
        use: {
          loader: 'svg-url-loader',
          options: {}
        }
      },
      {
        test: /\.css$/,
        loader: ExtractTextPlugin.extract({
          fallback: "style-loader",
          use: {
            loader: 'css-loader',
            options: {
              minimize: true
            }
          }
        })
      },
    ]
  },
  devtool: 'eval-source-map',
  plugins: [
    new SaveHashes({
      path: path.join(__dirname, 'config')
    }),
    new ExtractTextPlugin({
      publicPath: '/dist/',
      filename: '[name].[hash].css',
      allChunks: true
    }),
    new webpack.HotModuleReplacementPlugin(),
    new webpack.DefinePlugin({
      'process.env': {
        'NODE_ENV': JSON.stringify('production')
      }
    })
  ]
}

if (isProd) {
  config.plugins.push(new webpack.optimize.UglifyJsPlugin());
}

module.exports = config 
```

Enter fullscreen mode Exit fullscreen mode

我们的 Webpack 配置将确保客户端文件夹中的资产被编译成压缩的 JS 和 CSS 包，并带有缓存破坏哈希文件名。

现在我们必须让 Express 知道我们正在使用 Webpack，并且我们希望在启动时运行它。因此在 app.js 中添加以下内容:

```
var webpack = require('webpack')
var webpackDevMiddleware = require('webpack-dev-middleware')
var webpackHotMiddleware = require('webpack-hot-middleware')
var webpackAssets = require('express-webpack-assets')
.
.
.
// webpack setup
if (NODE_ENV === 'production') {
  app.use(express.static(__dirname + '/dist'));
} else {
  const compiler = webpack(config)
  app.use(webpackDevMiddleware(compiler, {
    publicPath: config.output.publicPath,
    stats: { colors: true }
  }))
  app.use(webpackHotMiddleware(compiler))
}
app.use(webpackAssets('./config/webpack-assets.json', {
  devMode: NODE_ENV !== 'production'
}));
.
.
. 
```

Enter fullscreen mode Exit fullscreen mode

**PostgreSQL**

最后，让我们通过安装[序列](http://docs.sequelizejs.com/) ORM 和相关的依赖项:
来添加 pg 支持

```
$ npm install sequelize pg pg-hstore --save
$ npm install sequelize-cli --save-dev
$ ./node_modules/.bin/sequelize init 
```

Enter fullscreen mode Exit fullscreen mode

运行这些命令将创建一些设置代码，您只需要用正确的连接信息更新您的`config/config.json`:

```
{  "development":  {  "username":  "root",  "password":  null,  "database":  "pevn_development",  "host":  "127.0.0.1",  "dialect":  "postgres"  },  "test":  {  "username":  "root",  "password":  null,  "database":  "pevn_test",  "host":  "127.0.0.1",  "dialect":  "postgres"  },  "production":  {  "username":  "root",  "password":  null,  "database":  "pevn_production",  "host":  "127.0.0.1",  "dialect":  "postgres"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

一旦有了这些，我们就可以创建我们的第一个模型并运行迁移:

```
$ ./node_modules/.bin/sequelize model:generate --name Visitor --attributes ip:string,user_agent:string
$ ./node_modules/.bin/sequelize db:create
$ ./node_modules/.bin/sequelize db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

对于这个例子，我们将只创建一个 Visitors 表，它将在您每次访问主页时记录用户的 IP 和 UserAgent 字符串，并显示最后 10 条记录:

```
var express = require('express');
var models = require('../models');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
  models.Visitor.create({
    user_agent: req.get('User-Agent'),
    ip: req.ip,
  }).then(() => {
    models.Visitor.findAll({limit: 10, order: [['createdAt', 'DESC']]}).then((visitors) => {
      res.render('index', { title: 'PEVN Stack!', visitors: visitors });
    })
  });
});

module.exports = router; 
```

Enter fullscreen mode Exit fullscreen mode

**结论**

这样我们就兜了一圈，结束了这个循环。如果一切正常，您现在应该能够在端口 4000 上运行您的应用程序，使用:

```
$ npm start 
```

Enter fullscreen mode Exit fullscreen mode

你可能会注意到的一件事是，每次你改变服务器代码时，应用程序都需要重新启动，这很烦人。我们可以改用 nodemon，这样当代码改变时，应用程序可以自动重启:

```
$ npm install --save-dev nodemon 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`nodemon.json`中，我们可以配置它在检测到服务器端逻辑发生变化时重启:

```
{  "verbose":  true,  "ignore":  ["public/"],  "events":  {  "restart":  "osascript -e 'display notification \"App restarted due to:\n'$FILENAME'\" with title \"nodemon\"'"  },  "watch":  ["routes/"],  "env":  {  "NODE_ENV":  "development"  },  "ext":  "js jade"  } 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以将 npm 启动命令更新为`nodemon app.js`

我们还可以做一些更有趣的事情，我在这个快速入门中没有提到。例如，我们可以通过 Babel 运行 NodeJS 服务器逻辑，这样我们也可以在服务器上使用 ES6 语法。我期待着从社区中获得这类改进的请求！:)

*这篇文章最初发表在[我的博客](https://jes.al/2018/02/getting-started-with-express-vuejs-postgresql/)上。如果你喜欢这篇文章，请在社交媒体上分享，并在推特上关注我！*