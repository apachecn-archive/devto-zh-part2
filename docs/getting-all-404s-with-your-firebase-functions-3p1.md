# 获得所有 404 的 firebase 功能？

> 原文：<https://dev.to/splodingsocks/getting-all-404s-with-your-firebase-functions-3p1>

如果您正在使用 Firebase 云函数，并且您会得到如下错误:

`cannot GET null`尝试在生产中访问您的函数后，可能是因为您使用的是一个 Express app a 请求处理程序，firebase 传入的请求对象有一个空字符串，而不是一个“/”作为 URL(直接访问函数名时不带尾随斜杠)。

诀窍是截取请求，并在之前插入“/”*，然后将它传递给 Express 应用程序。我试着把它作为应用程序的中间件，但它不起作用。*

下面是我的补丁:

```
let firebasePathPatch: 'a => Express.App.t = [%bs.raw {|
  app => (req, res) => {
    // patch from https://github.com/firebase/firebase-functions/issues/27#issuecomment-292768599
    // https://some-firebase-app-id.cloudfunctions.net/route
    // without trailing "/" will have req.path = null, req.url = null
    // which won't match to your app.get('/', ...) route 
    if (!req.path) {
      // prepending "/" keeps query params, path params intact
      req.url = `/${req.url}`
    }
    return app(req, res);
  }
|}]; 
```

而且是这样用的:
`Firebase.CloudFunctions.functions##https##onRequest(app |> firebasePathPatch);`

上面的片段是合理的，这里有一个 JS 版本:

```
let firebasePathPatch = app => (req, res) => {
    // patch from https://github.com/firebase/firebase-functions/issues/27#issuecomment-292768599
    // https://some-firebase-app-id.cloudfunctions.net/route
    // without trailing "/" will have req.path = null, req.url = null
    // which won't match to your app.get('/', ...) route 
    if (!req.path) {
      // prepending "/" keeps query params, path params intact
      req.url = `/${req.url}`
    }
    return app(req, res);
  }

// Used like this:
module.exports.foo = functions.https.onRequest(firebasePathPatch(app)); 
```

更多请看这里:[https://github.com/firebase/firebase-functions/issues/27](https://github.com/firebase/firebase-functions/issues/27)