# 从 Chrome 的控制台轻松复制对象和数组

> 原文：<https://dev.to/changoman/easily-copy-objects-and-arrays-from-chromes-console-56m5>

当我们以对象或数组的形式获取一些数据时，很难从 Chrome 的控制台复制并粘贴结果。如果您试图突出显示那里的内容，那么当您粘贴时，您只会得到类似于`Array(3)`的东西，而不是实际的数据。

我刚刚学会了从 Chrome 的控制台复制东西的简单方法。

当您在控制台中看到数据时，右键单击它以存储为全局变量。

[![](img/cd276822497759990def80d0c0ea89e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6BxKRz7L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://content.screencast.com/users/hunter1291/folders/Jing/media/b941542b-cd4b-4b56-b207-11a194c861e5/00000543.png)

Chrome 会将其存储为一个名为`temp1`的临时变量

[![](img/e6ad9d1304b4dfe8d1bc06c6f2bfefe9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--51dLH21U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://content.screencast.com/users/hunter1291/folders/Jing/media/41440fc3-50e1-4d38-b791-3744ac4f50ae/00000542.png)

一旦有了变量，只需使用`copy()`函数。

```
copy(temp1) 
```

Enter fullscreen mode Exit fullscreen mode

敲回车你会看到`undefined`就在它的正下方，这很正常。

[![](img/e716c424b42cf5ae1ea5baa6947265d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NjF9CKck--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://content.screencast.com/users/hunter1291/folders/Jing/media/6580041f-c227-4413-a191-779d04dff29d/00000544.png)

现在你已经有了剪贴板上的所有数据，只要把它粘贴到你喜欢的任何地方！

```
[
  {
    "data": "data for res #1"
  },
  {
    "data": "data for res #2"
  },
  {
    "data": "data for res #3"
  }
] 
```

Enter fullscreen mode Exit fullscreen mode