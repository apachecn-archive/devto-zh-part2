# 我知道你一直希望你的桌面应用程序使用 CSS！

> 原文：<https://dev.to/havardh/i-know-you-always-wanted-css-for-your-desktop-applications-4b0f>

工作流程 + [瑜伽](https://yogalayout.com/) + react =你的桌面应用的 css 布局。技术上来说只有 Flexbox。你一直都想这样，对吧？看看那些间隔很好的应用程序窗口吧！

[![floating-images](img/4d5151cb3a150a9bb10bea377fff42f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--POFiy9UX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/50qeakmeyszs5ii28v92.png)

也许没有，至少我从来没有。但是，嘿，让我们不要沉迷于我们想要建立什么，而是看看我们能建立什么。

如果你是第一次接触 [`workflow`](https://github.com/havardh/workflow) ，那么[介绍帖](https://dev.to/havardh/introducing-workflow-18h6)是个不错的开始。

## 使用它

上面的桌面布局是通过列出一个文件夹中的所有图像文件并在图像浏览器`xv`中打开它们而生成的。屏幕上的大小和位置是使用`style`属性定义的，就像在 web 上一样。这是通过下面的代码片段实现的。点击查看完整示例[。](https://github.com/havardh/workflow/blob/master/packages/workflow-app-xv/flows/Example.js)

```
<Workspace name={'workflow-pictures'}>
  <Flex
    style={{
      width: '100%',
      height: '100%',
      flexDirection: 'row',
      flexWrap: 'wrap',
   }}
  >
    {files.map(file => (
      <XV
        style={{ height: '300px', width: '450px', margin: '30px' }}
        file={join(folder, file)}
      />
    ))}
  </Flex>
</Workspace> 
```

[`workflow-layout-yoga`]((https://github.com/havardh/workflow/tree/master/packages/workflow-layout-yoga)) 包导出两个布局节点`Yoga`和`Flex`，它们可用于使用 Flexbox 指定布局。这些节点可以在布局内的任何地方使用，并与来自 [`workflow-layout-tiled`]((https://github.com/havardh/workflow/tree/master/packages/workflow-layout-tiled)) 的`SplitV`和`SplitH`节点混合使用。

`folder`和`files`变量包含一个文件夹的路径和该文件夹中的一组文件。

## 集成工作流和 yogalayout

Yoga 采用带有 Flexbox 布局注释的节点树，并在屏幕上返回带有绝对位置的树。工作流的内部[布局模块](https://github.com/havardh/workflow/tree/master/packages/workflow-layout)，让我们用绝对坐标指定应用程序在屏幕上的位置。`custom`布局节点类型让`workflow-layout-yoga`向`workflow-layout`传递一个函数，该函数将被评估以将 Flexbox 布局转换为内部`workflow`节点格式。如果你想用其他方式来扩展`workflow`，那么`workflow-layout-yoga`包就是一个很好的例子。

来参加 GitHub 和 T2 的乐趣吧。:)

安德烈斯·戈麦斯、[沃尔特·张](https://unsplash.com/@waltercheungsg)、[王茜](https://unsplash.com/@wx1993)、[本杰明·阿切利](https://unsplash.com/@benjaminatchley)、[阿格涅什卡·科瓦尔茨克](https://unsplash.com/@koalamoose)和[帕韦·切维斯基](https://unsplash.com/@pawel_czerwinski)在 Unsplash 上的照片