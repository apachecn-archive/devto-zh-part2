# 使用 React 平滑上传文件

> 原文：<https://dev.to/rikschennink/smooth-file-uploading-with-react-4h3f>

**关于如何快速设置 FilePond React 适配器的教程**

[![](../Images/2a13f1be0f1dbf4e19413386a5d32ee8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OeuBUGdA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AA66M6hR0oBaAqNoLqBXZ9A.jpeg)

[FilePond](https://pqina.nl/filepond) 是一个比较新的 JavaScript 文件上传库。在本教程中，我们将建立一个新的 [React](https://reactjs.org) 应用程序，与 [FilePond React 适配器](https://github.com/pqina/react-filepond)组件集成，并建立一个到后端的连接，该后端将接收我们上传的文件。

我们将使用 [Create React App](https://github.com/facebook/create-react-app) 节点模块来快速组装我们的基本 React 应用程序。如果您熟悉这个过程，您可以跳到与 FilePond 集成一节。

### 创建基础 React 应用

假设您已经安装了[节点](https://nodejs.org/)版本 6 或更高版本，从您的终端运行以下命令(如果没有，现在是安装它的时候了)。它将创建一个文件夹“我的应用程序”，其中将包含我们的反应应用程序。

```
npx create-react-app my-app 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们有了起点，让我们导航到“我的应用程序”目录。

```
cd my-app 
```

Enter fullscreen mode Exit fullscreen mode

现在启动开发服务器，它会自动打开一个浏览器窗口。

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过同时按下`CTRL`和`C`键从命令行停止应用程序。

### 与 FilePond 集成

随着 React 应用程序的运行，我们现在可以添加 FilePond React 适配器。

让我们停止应用程序并安装适配器。

```
npm install filepond  react-filepond --save 
```

Enter fullscreen mode Exit fullscreen mode

安装好适配器文件后，让我们再次启动应用程序，这样我们就可以开始进行更改了。

我们将 FilePond 组件添加到应用程序登录页面，实际上，您可能会将它移动到其他地方，但对于本文的目的来说，这已经足够了。

我们首先需要导入`FilePond`组件及其样式表。

打开“App.js”文件，将以下几行添加到文件的顶部。

```
import { FilePond } from 'react-filepond';
import 'filepond/dist/filepond.min.css'; 
```

Enter fullscreen mode Exit fullscreen mode

下一步是将组件添加到 HTML 中。让我们在`render()`函数中编辑 HTML 并添加`<FilePond>`标签。

```
<div className="App">

  <header className="App-header">
    <img src={logo} className="App-logo" alt="logo" />
    <h1 className="App-title">Welcome to React</h1>
  </header>

  <p className="App-intro">
    To get started, edit <code>src/App.js</code> and save to reload.
  </p>

  <FilePond></FilePond>

</div> 
```

Enter fullscreen mode Exit fullscreen mode

文件池拖放区现在应该显示在介绍文本的下面。它看起来会像这样:

[![](../Images/da3253e2381b27b2f57eba01b01c8002.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--byiTdyht--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2AtMmUHba39VqaKbCyry9K8A.png)

现在，我们可以开始按照自己的意愿配置 FilePond 了。

默认情况下，FilePond 只接受一个文件，这是因为它增强了默认的 file input 元素并复制了它的标准行为。

[![](../Images/21335b38b025e7451cc448eed38c85be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bS78kTgx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2A27IG4zyokQcRTxh2zU6YWg.gif)

要启用多文件模式，让我们添加`allowMultiple`属性。

```
<FilePond allowMultiple={true}></FilePond> 
```

Enter fullscreen mode Exit fullscreen mode

放下一个文件夹或选择多个文件来查看它的运行。

[![](../Images/25d1cce70ca139e99f2ae4b41df9a48f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kuqW4QX0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2AEQ3uuEJ4nwtsBH5-5GYGYA.gif)

就像经典的文件输入一样，文件被加载到 FilePond，但不做任何其他事情，它们只是呆在那里。我们很可能希望将它们发送到我们的后端。我们将通过为 FilePond 提供服务器属性来实现这一点。

如果你有一个服务器端点，可以处理发送给它的文件对象，你可以使用它，如果没有，我们可以设置 [FilePond PHP 样板文件](https://github.com/pqina/filepond-boilerplate-php)(下载存储库并运行 vagger up)。

```
<FilePond allowMultiple={true} server="http://192.168.33.10"></FilePond> 
```

Enter fullscreen mode Exit fullscreen mode

配置好服务器位置后，FilePond 会自动将拖放的文件发布到提供的 URL。

[![](../Images/196de204436b91abad8c65a04d09e679.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RB4p6huM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2A8tN-yfQKQPkkHfM07T5NQg.gif)

默认的文件池服务器调用在[服务器配置文档](https://pqina.nl/filepond/docs/patterns/api/server/)中有描述。属性的每一个微小细节都可以被微调，这使得 FilePond 基本上可以与任何远程或本地文件存储解决方案集成。

为了增加趣味，让我们启用图像预览。

我们将从安装图像预览插件开始。

```
npm install filepond-plugin-image-preview --save 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要导入并向 FilePond 核心注册插件。我们将不得不修改 FilePond 导入来导入`registerPlugin`方法。

```
import { FilePond, registerPlugin } from 'react-filepond'; 
```

Enter fullscreen mode Exit fullscreen mode

我们已经准备好导入和注册图像预览插件。

```
import FilePondPluginImagePreview from 'filepond-plugin-image-preview';
import 'filepond-plugin-image-preview/dist/filepond-plugin-image-preview.min.css';

registerPlugin(FilePondPluginImagePreview); 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。让我们重新启动应用程序，并删除一个图像。

[![](../Images/02434c390875df450ba31533ebeaafc1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_EGElTS7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/900/1%2AmwkOVJH-HlWwIMYa6xkRHw.gif)

这就是用 FilePond 上传文件的基础。

你可以通过自动 [EXIF 方向校正](https://github.com/pqina/filepond-plugin-image-exif-orientation)、[图像裁剪](https://github.com/pqina/filepond-plugin-image-crop)、[大小调整](https://github.com/pqina/filepond-plugin-image-resize)、[客户端图像变换](https://github.com/pqina/filepond-plugin-image-transform)和[各种其他插件](https://github.com/pqina/filepond#plugins)来进一步增强组件。

你可以配置很多[道具](https://pqina.nl/filepond/docs/patterns/api/filepond-instance/#properties)和[事件](https://pqina.nl/filepond/docs/patterns/api/filepond-instance/#callbacks)来让 FilePond 满足你的需求。

* * *