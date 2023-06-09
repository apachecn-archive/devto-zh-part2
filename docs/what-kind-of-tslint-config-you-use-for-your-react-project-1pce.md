# React 项目使用哪种 tslint 配置？

> 原文：<https://dev.to/buinauskas/what-kind-of-tslint-config-you-use-for-your-react-project-1pce>

刚开始看 TypeScript，React，想为它准备一个不错的工作环境。

我从
开始

```
create-react-app my-app --scripts-version=react-scripts-ts 
```

Enter fullscreen mode Exit fullscreen mode

添加了以下依赖关系

```
yarn add -D prettier tslint-config-prettier tslint-plugin-prettier husky pretty-quick 
```

Enter fullscreen mode Exit fullscreen mode

让漂亮的人知道我想要单引号，而不是双引号:

```
{  "singleQuote":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

在我的`tslint.json`中添加了以下内容:

```
{  "extends":  [  "tslint-react",  "tslint-plugin-prettier",  "tslint-config-prettier"  ],  "rules":  {  ...,  "prettier":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然后在我的`package.json` :
中添加了下面一行

```
{  "scripts":  {  ...,  "precommit":  "pretty-quick staged"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

对我自己来说，这看起来确实不错。格式化留给更漂亮的，林挺由 tslint 完成。你使用哪种设置？