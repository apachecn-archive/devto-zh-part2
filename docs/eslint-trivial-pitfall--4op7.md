# Eslint 琐碎的陷阱。

> 原文：<https://dev.to/hirodeath/eslint-trivial-pitfall--4op7>

嗨！我是 hiro，我是前端开发人员，但是我最近非常喜欢使用 Node.js 进行服务器端开发。

所以，这是我的第一篇文章。我会每天定期给开发者发文章。
我不知道这是否会很难，但对我来说，继续张贴它是一个很好的挑战。

是时候完成自我介绍了！开始今天的话题吧！

## Eslint 琐碎的 pithall。

我在做两份工作，一份是主要工作，部分使用 React.js(但这不是一个小范围的领域，它正在日益扩大)。第二是[在一起](https://togetter.com/)并使用反应式。常见的是利用反应生态系统和现代环境。

Eslint 不是林挺图书馆的标准吗？但我甚至没有意识到琐碎的陷阱。

当你指定你的 eslint 时，你可以把这个代码写到`npm scripts`里面的`package.json`。

```
"scripts": {
  "lint": "eslint src/js/* ",
} 
```

Enter fullscreen mode Exit fullscreen mode

我认为这是正确的。但是，如果您想更深入地创建如下所示的目录层次结构，该怎么办呢？

```
src/  
  js/  
    components/
      SomeComponents/
        index.jsx
        style.js
    index.js 
```

Enter fullscreen mode Exit fullscreen mode

首先，你可以写下面的代码。

```
"scripts": {
  "lint": "eslint src/js/**/*.jsx src/js/**/*.js ",
} 
```

Enter fullscreen mode Exit fullscreen mode

这个不好用。但是如果你写`eslint src/js/**/**/*.jsx src/js/**/**/*.js`，它将工作，但是如果你更深入地研究制作目录，你需要添加更多的路径位置或者像`/**/`一样添加通配符。
另外，当您指定`eslint src/js/*`时，将只检查`*.js`文件。如果你想检查更多的`*.jsx`呢？

超级简单的解决方案。
你只需要添加如下。

```
"scripts": {
  "lint": "eslint src/js/* --ext .js --ext .jsx ",
} 
```

Enter fullscreen mode Exit fullscreen mode

这将按照您的预期工作！我已经陷入了类似这个问题的陷阱。虽然我不仅仅是意识到了 eslint 配置，但是它是一个很棘手的问题。

祝你编程愉快！