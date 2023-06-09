# 米德卡麦得制作的是什么？如何使用和使用？

> 原文：<https://dev.to/tuantnguyen/middleware-trong-expressjs-l-g-tc-dng-v-cch-s-dng-nh-th-no-3i2j>

*   正如我们在一般软体中使用的明尼布作为一个样子？就像我说的一样
*   正如我们在网站上使用的明尼布的 30 处理器一样？就像我说的一样
*   [中间件 trong expressjs](#middleware-express)

# 问题

就是说，当我们找到一个适合使用 1230 分布的运算函数的话，有没有用来描述一个真正的极限

> 在指定的路径上安装指定的一个或多个中间件功能:当请求的路径的基址与路径匹配时，执行中间件功能。

就想了一个不明确的时候也没弄清楚到底为什么我们只能找到一个 1230 磅的描述。我虽不知道怎么找到并写成博客但我觉得有点复杂。

## 明德铁制在软件行业是什么？

> 明德钢制是连接软件组件或应用程序的计算机软件。这个软体包括一系列服务，可让您在一个或多个不同机器上进行互动。明德钢制技术已经发展到提供相互支持的能力，为分散式建筑提供经常支持和简化的分散应用。就是使用一个适合使用 1231 分布的电子游戏来弄一个适合我使用的电子游戏描述的网站

*   明德钢锭是位于 OS 的操作系统和应用程序之间的。旨在管理数据管理和沟通的瓦特 apps

    > 仓促式、apps 是运行在多个计算器上的应用程序或软件，但是在同一个网络系统中。不像传统的 apps 只运行单个系统(单个系统)，而在远程控制模块上，一个任务是同一个任务或某个任务。

*   可以把明德的铁渣想像成管状的管子。这些管子将是把这些电缆连接在一起的地方，从这个地方收起头来，然后把水运到头上的意愿。正在发布一个差异的数据库使其有两个应用程序联系起来使得数据库或者可以轻松地通过这些管子交换。就为了使用一个适合我使用的网站来讲解一个 1233 适合使用的电子游戏

## 明德在网站上的应用如何？

*   它是数据库和应用程序之间的桥梁
*   处理需要并决定了三重返程序的回应
*   也可以是色差的连接桥
*   也可以控制应用程序在错误时的状态
*   讯息输入到应用程式去处理和使用者的应用程式

## 中间件 trong expressjs

> Express 是一个路由和中间件 web 框架,它拥有自己的最小功能:Express 应用程序本质上是一系列中间件函数调用

因此，通常使用的是明德函数的一系列函数。

中间件 trong expressjs là:

*   已经执行一连串函数，由「图层路由器」呼叫
*   函数收到了请求的函数，已经有 1 个请求/3 的回应期了
*   函数可能是已经修复了的请求，之前将它们发送到了 1230 弄来的明显的函数
*   有必要的话也许会更新或结束
*   函数的作用是由以下三个参数的物理对象构成的:12331 块处理方式提供的？因此要求 1233 对象是使用同样的方法进行的？使用同样的方法 1234？因此，函数的作用也是下面的一个比较差的:

```
function logger(req,res,next){
  console.log(new Date(), req.method, req.url);
  next();
} 
```

Enter fullscreen mode Exit fullscreen mode

[![middle component](img/6078c66443edc2713a1b6d2aa8397002.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IQmv2f2d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vs9wjgjvrq9drwnn94kk.png)

### 就为了使用一个差不多 1231 分钟的描述方式绘制一个适合我使用的网站

*   明德的钢琴器被用来做以下工作:

    *   任何代码都可以执行
    *   更新请求，响应
    *   结束了三个月的服务请求。要是有些天然运动发生的话不是用本地的函数来弄一个 1230 分钟的样子来结束我们就可以直接结束了
*   高速应用程序可以使用以下的明德类

    *   正如我们正在使用的正确的使用程序使用的凌晨 1 . 233 弄明显的应用程序使用正确的应用程序使用这个游戏使用程序 1235 适合使用一个电子游戏弄来使用相同的极致使用原样的游戏来使用此程序
    *   就是使用一个讲明适合使用的网路版本的明确讲解码器 3230 使用的方式，除非有适用同样的配方使用同样的电子游戏法，除非适用同样的配置
    *   正在处理一个显示自身使用的明显处理器系统上的图案
    *   [内置中间件](https://expressjs.com/en/guide/using-middleware.html#middleware.built-in)
    *   [第三方中间件](https://expressjs.com/en/guide/using-middleware.html#middleware.third-party)

```
var express = require('express')
var app = express()
var cookieParser = require('cookie-parser') // lib bên thứ ba

// load the cookie-parsing middleware
app.use(cookieParser()) 
```

Enter fullscreen mode Exit fullscreen mode

### 就如同我们使用一个简单的方法来解释一个差异的游戏

*   数学:创建使用当前时代的明德圆珠并将一本书保存到数据库和渲染消息
*   歌曲:

1/ Tạo 法 cho 测井当前时间

```
 //create a method to
    var requestTime = function (req, res, next) {
      req.requestTime = Date.now();
      console.log("Current Time for Request"+req.requestTime );
      next()
    }; 
```

Enter fullscreen mode Exit fullscreen mode

2/使用上面的明珠钢索作为明显的使用器具套盘的 330 弄得严密

```
//use the middleware as an application-level middleware
app.use(requestTime); 
```

Enter fullscreen mode Exit fullscreen mode

三分之三的明德使它连接到 tips(tips _ 1230)上

```
// method to conenct to be

var conencttodb = function(req, res, next){
  try
  {
    var path= 'mongodb://localhost:27017/library';
    connect = mongoose.connect(path);
    console.log('conencted to db!!');
    //mongoose.set('debug', true);
  }
  catch(e)
  {
    console.log(e);
  }
};
app.use(conencttodb) 
```

Enter fullscreen mode Exit fullscreen mode

使用适合使用的 y 型电子游戏使用的本书 4/1230 来储存资讯

```
// create a new book
var book = new Book({
  title: 'Peter Quill',
  author: 'starlord55',
  yearpubished: 2011,
  id: 101,
  pages:56,
  ratings:1
});

// save the book in database
book.save(function(err, req, res) {
  console.log(req.body);
  if(err) {
    console.log(err);
    res.send({
      message :'something went wrong'
    });
  } else {
    // res.contentType('application/json');
    res.send({
      message:'the book has bees saved at'+req.requestTime
    });
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 下一步做什么

继续玩家节省=-