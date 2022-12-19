# 在 Sentry 中跟踪错误(在 node.js 中)

> 原文：<https://dev.to/mogery/tracking-errors-in-sentry-in-nodejs-4d1o>

# 简介

Sentry 是一个开源的错误跟踪工具，可以帮助开发人员实时监控和修复崩溃。设置真的很快，支持很多平台和编程语言。

在 sentry 的 Web UI 上，您可以看到代码捕获的所有错误、堆栈跟踪、上下文和面包屑(如果您实现了它)。

# 设置您的哨兵项目

在[哨兵](https://sentry.io)上注册并登录后，按右上角的新建项目。

[![alt-image](img/751c46cf1c5a2ddf7d8eb817574b49d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H_xeNVXF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.gyazo.com/ab375fd14b1381590eb2d62a7f1356e5.png)

在本教程中，我们将介绍 Sentry 的一般 Node.js 用法，因此请确保选择 Node.js 而不是 Express。

在给你的项目命名并点击闪亮的 Create 按钮后，会弹出一个文档页面。

[![alt-image](img/6a4942f81bfe74e8213f0ebf1d9fe78e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8RVf-u19--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.gyazo.com/24f3e14b1606c9f353d9650eb6761f30.png)

在“配置客户端”标题下，应该有一个包含您的 Sentry DSN 的代码块。将代码保存在某个地方，因为**我们稍后会需要它**，所以我们可以通过我们的客户端模块 Raven 与 Sentry 通信。

# 设置渡鸦

在 Sentry 网站上创建项目后，是时候启动编辑器并开始编码了。

首先，您需要将 Raven 模块添加到项目中。

```
npm install raven --save 
```

Enter fullscreen mode Exit fullscreen mode

打开您之前从 Sentry 网站保存的代码，并将其与文件中的其他 require 语句一起粘贴。为了让代码**为公共 Git** 提供者做好准备，不要忘记**移除硬编码的 Sentry DSN** ，并将其移动到外部 gitignored 配置文件，或者移动到**环境变量**。

# 捕捉错误

让我们用一个示例代码来展示捕捉错误的不同方法。

```
function doSomething(data) {
  if (data) { // Check if our data is defined or not
    return data; // Return data
  } else {
    throw new Error("Missing required data parameter."); // Throw error
  }
}

doSomething(); // Call doSomething without defining data -> error thrown 
```

Enter fullscreen mode Exit fullscreen mode

我们的函数`doSomething`将检查它的参数`data`是否被定义:如果是，它就返回它，如果不是，**它抛出一个错误**。在定义了`doSomething`之后，我们的代码在没有提供数据参数的情况下调用了它**，因此抛出了一个错误。**

有两种主要方法来捕获 Raven 客户端的问题:

1.  将代码包装到上下文中，或者
2.  手动捕获错误。

## 方法 1——将代码包装到上下文中

有两种将代码包装到上下文中的方法:`Raven.context`和`Raven.wrap`。除了`Raven.wrap` **返回一个函数**，而`Raven.context`不返回之外，没有太多的区别，这意味着您可能希望将`Raven.wrap`用于回调之类的代码，而将`Raven.context`用于一般的代码包装。

```
var Raven = require('raven');

Raven.config('https://13c3c4f3c6094d749436722b3031f787:6fe68b76c7e34d2cb69831355d6e3beb@sentry.io/301629').install();

function doSomething(data) {
  if (data) { // Check if our data is defined or not
    return data; // Return data
  } else {
    throw new Error("Missing required data parameter."); // Throw error
  }
}

Raven.context(function() { // Wrap the following code with Raven.context
  doSomething(); // Call doSomething without defining data -> error thrown
});

setTimeout(Raven.wrap(function() {
  doSomething(); // Call doSomething without defining data -> error thrown
}, 1000); // Run doSomething delayed with setTimeout and Raven.wrap 
```

Enter fullscreen mode Exit fullscreen mode

使用这种方法而不是手动捕获错误有一个很大的优势:上下文中的代码(也称为`Raven.wrap`或`Raven.context`中的代码)可以**访问能够将数据与上下文**相关联的方法，这对于调试很有用。

```
var Raven = require('raven');

Raven.config('https://13c3c4f3c6094d749436722b3031f787:6fe68b76c7e34d2cb69831355d6e3beb@sentry.io/301629').install();

function doSomething(data) {
  if (data) { // Check if our data is defined or not
    return data; // Return data
  } else {
    throw new Error("Missing required data parameter."); // Throw error
  }
}

Raven.context(function() { // Wrap the following code with Raven.context
  Raven.setContext({ // Set context data with the specified object
    tags: {
      component: 'main'
    }
  });
  Raven.mergeContext({ // Extend context data with the specified object
    tags: {
      method: 'doSomething'
    }
  });
  console.log(Raven.getContext());

  doSomething(); // Call doSomething without defining data -> error thrown
}); 
```

Enter fullscreen mode Exit fullscreen mode

请记住，`setContext`、`mergeContext`和`getContext`、**只在上下文**中的代码**中起作用**。

您可以在上下文数据中存储任何内容，例如，您可以将错误与用户数据相关联，如下所示:

```
Raven.setContext({
  user: {
    email: 'matt@example.com',
    id: 123
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 方法 2 -手动捕捉错误

使用 Raven 的`caputreException`，您可以捕获**非抛出异常**，或者您可以使用 **try-catch** 块捕获抛出异常。

```
var Raven = require('raven');

Raven.config('https://13c3c4f3c6094d749436722b3031f787:6fe68b76c7e34d2cb69831355d6e3beb@sentry.io/301629').install();

function doSomething(data) {
  if (data) { // Check if our data is defined or not
    return data; // Return data
  } else {
    throw new Error("Missing required data parameter."); // Throw error
  }
}

try {
  doSomething(); // Call doSomething without defining data -> error thrown
} catch (e) {
  Raven.captureException(e, function(sendErr) { // Capture exception
    if (sendErr) { // Check if send failed
      console.error("Failed to send exception to Sentry.");
    } else {
      console.log("Exception sent to Sentry.");
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以用`captureMessage`到**发送非异常错误消息**给哨兵。

```
var Raven = require('raven');

Raven.config('https://13c3c4f3c6094d749436722b3031f787:6fe68b76c7e34d2cb69831355d6e3beb@sentry.io/301629').install();

function doSomething(data) {
  if (data) { // Check if our data is defined or not
    return; // Return nothing
  } else {
    return "Missing required data parameter."; // Return error
  }
}

var err = doSomething(); // Call doSomething without defining data -> error returned
if (err) {
  Raven.captureMessage(err, function(err) {
    // Sent!
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然您**没有访问**到`setContext`和其他仅上下文相关的功能，但是您可以**向`captureException`和`captureMessage`提供应该被捕获的附加数据**。

```
Raven.captureException(err, {
  user: { id: 123 }, // User-related info
  req: req, // Request object from HTTP web server (handled by Raven Express)
  tags: { component: 'main', method: 'doSomething' }, // Tags
  extra: { catType: cat.type }, // Any other data you'd specify with setContext
  level: 'error' // Event level
}); 
```

Enter fullscreen mode Exit fullscreen mode

如需更多信息，请访问[Raven Node 文档](https://docs.sentry.io/clients/node/usage/#additional-data)。

# 面包屑，面包屑遍地！

对于在异常发生之前的数据*的附加信息，面包屑是解决方案。面包屑是发生在你的应用程序**中的事件**的**轨迹，导致一个被捕获的错误**。它们可以像日志消息一样简单，也可以包含关于应用程序状态的丰富元数据，比如网络请求、数据库查询，甚至是之前发生的错误。*

Raven for Node 支持一个名为`autoBreadcrumbs`的特性，**自动记录**有用的面包屑，像 HTTP(S)请求，日志语句(带`console.log`，`.warn`等。)，并通过`pg`模块进行 PostgreSQL 查询。

要启用它，将此设置添加到`Raven.config`，您就可以开始了！

```
Raven.config('https://13c3c4f3c6094d749436722b3031f787:6fe68b76c7e34d2cb69831355d6e3beb@sentry.io/301629', {
  autoBreadcrumbs: true // Enable autoBreadcrumbs
}).install(); 
```

Enter fullscreen mode Exit fullscreen mode

这可能对你来说足够了，但是如果不够，不用担心！有一种简单的手动捕获面包屑的方法。

```
var Raven = require('raven');

Raven.config('https://13c3c4f3c6094d749436722b3031f787:6fe68b76c7e34d2cb69831355d6e3beb@sentry.io/301629').install();

function doSomething(data) {
  if (data) { // Check if our data is defined or not
    return data; // Return data
  } else {
    throw new Error("Missing required data parameter."); // Throw error
  }
}

var ourJson = "{}"; // Blank JSON object, not containing needed data.

Raven.context(function() { // Wrap the following code with Raven.context
  var parsedJson = JSON.parse(ourJson); // Parse JSON
  Raven.captureBreadcrumb({ // Capture breadcrumb
    message: 'Parsed JSON',
    category: 'log',
    data: {
      raw: ourJson,
      parsedJson: parsedJson
    }
  });
  doSomething(parsedJson.data); // Call doSomething with non-existing data -> error thrown
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在 Web UI 上查看错误，我们可以看到包含原始 JSON 和解析的 JSON 的面包屑，这样我们可以看到 JSON 是否没有所需的数据(这里就是这种情况)，或者我们是否解析了错误的 JSON。

这对于调试来自外部来源的信息非常有用，因此您可以看到您的代码是否是错误的，或者用户的输入是否是错误的。