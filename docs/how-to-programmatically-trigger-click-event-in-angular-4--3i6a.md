# 如何在 Angular 4 & 5 中以编程方式触发“Click”事件

> 原文：<https://dev.to/gloriamaris/how-to-programmatically-trigger-click-event-in-angular-4--3i6a>

Angular 4(和 5)中的资源是稀缺的。真的。有时候，我在筛选堆栈溢出时迷失了，以至于我最终使用最新的 Angular 文档翻译了我在旧版本(1.x 和 2.x)中找到的答案。

我正在做的项目中的一个任务涉及到使用拖放框或点击*浏览*来上传文件，就像这样:

[![File Upload Page](../Images/df450a750cd4ddf0dc3fa5d6ec613e1b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LG8EzHgG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/se3lwt047zm6w7pchsq8.png)

本质上，我想在点击*浏览*(这是一个锚标签)时触发隐藏在页面中的文件输入按钮来打开文件浏览器。

对于像我这样的新手，我最初求助于在 NPM 搜索软件包，只知道有些只是勉强维护，没有测试与 Angular 新版本的兼容性，或者有太多我不需要的功能。

意识到我的错误后，我决定寻找一个更简单的答案，但它们中的许多仍然需要我导入模块，这使项目变得臃肿。

在开始之前，这里是我的统计:

```
Angular CLI: 1.7.0-beta.3
Node: 8.9.4
OS: linux x64
Angular: 4.4.6
... animations, common, compiler, compiler-cli, core, forms
... http, language-service, platform-browser
... platform-browser-dynamic, router, tsc-wrapped

@angular/cli: 1.7.0-beta.3
@angular-devkit/build-optimizer: 0.2.0
@angular-devkit/core: 0.2.0
@angular-devkit/schematics: 0.2.0
@ngtools/json-schema: 1.1.0
@ngtools/webpack: 1.10.0-beta.3
@schematics/angular: 0.2.0
@schematics/package-update: 0.2.0
typescript: 2.3.4
webpack: 3.10.0 
```

Enter fullscreen mode Exit fullscreen mode

我特别不喜欢`angular.element`,因为它只是一个仍然使用 jQuery 的包装器，我喜欢尽可能地让我的代码免于导入我无法重用的内容。最终，我通过使用来自 [Web API](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement) 的接口`HTMLElement`找到了一个更有效的解决方案。

所以我是这样做的，给你(和未来的我)一个指导:

## 模板

页面视图被命名为`uploadFile.component.html`

[![Upload Image](../Images/fb6fed294c3592ccddb0517d2b75a78d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2XXFJG8w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3xtk11l34hx2cxlpzusw.png)

重要的东西:

*   类`.hide-style`包含`{display: none}`来隐藏文件输入。
*   id `#tenantPhotoId`是用于引用文件输入的标识符。
*   变量`tenantIDFileName`绑定要在页面上显示的文件的文件名。

## 组件

TS 文件被命名为`uploadFile.component.ts`

[![Upload Image](../Images/0b8adf54fb2bc235a1a1dc3ce5a0e3e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HX5oKy6W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/spwdwoj8xccu2ffbj3uz.png)

重要的东西:

*   方法`onFileChange($event)`通过给变量`tenantIDFileName`赋值来显示正在上传的文件的文件名。
*   方法`openFileBrowser($event)`触发文件浏览器上的 click 事件。

## 流程的预排

当点击*浏览*时，以`$event`为参数调用`openFileBrowser($event)`。这样我们就可以使用`preventDefault()`来防止锚标签重定向到它所指向的页面的正常行为。

然后，使用使用`document.getElementById()`输入的文件的 ID 获取该值，并将其转换为`HTMLElement`类型。

[HTMLElement](https://www.javascripture.com/HTMLElement) 表示文档中的任意元素，并实现*事件处理程序*接口，所以现在我们可以调用`click()`。成功地，文件浏览器被触发打开。

每当选择一个文件时，`onFileChange($event)`将被调用，`tenantIDFileName`变量将被指定为文件名的值，并显示在页面上。

**注意，在实际的文件上传过程中，您可以使用一个 API 端点将数据存储在您的服务器上，或者调用 AWS S3 等第三方服务。**

编辑:如果你找到了更好的解决方案，那么我们可以通过分享尽可能多的参考资料来互相帮助。:-)

这就对了。黑客快乐！