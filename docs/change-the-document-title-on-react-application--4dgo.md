# 在 react 应用程序上更改文档标题

> 原文：<https://dev.to/styluso7/change-the-document-title-on-react-application--4dgo>

作为 react 的初学者，我通过运行一个命令 create-react-app 来开发应用程序。

默认情况下，应用程序将生成以下标题

```
React App 
```

Enter fullscreen mode Exit fullscreen mode

这很容易改变，只要将这个生命周期与你的应用程序文件挂钩

```
 componentDidMount() {
    document.title = 'Custom Title';
  }
~~~~

Now your application title will be changed to your custom title 
```

Enter fullscreen mode Exit fullscreen mode