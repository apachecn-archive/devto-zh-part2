# 让 Ant Design CSS 优化在 CodeSandBox 上工作

> 原文：<https://dev.to/dance2die/making-ant-design-css-optimization-to-work-on-codesandbox-1n4m>

*照片由[纪尧姆·德·日耳曼](https://unsplash.com/photos/mrL7QWWkciE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/ant?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

我已经在 [CodeSandBox](https://codesandbox.io) 上创建了一些小网站，这样可以节省你安装新软件包的时间。

[Ant Design](https://ant.design) 通过 [react-app-rewired](https://github.com/timarney/react-app-rewired) 提供了一种优化组件& CSS 导入的方法，使用他们定制的 babel 插件( [babel-plugin-import](https://github.com/ant-design/babel-plugin-import) )，而不必弹出`create-react-app`生成的站点。

本地一切正常，但 CSS 没有应用于 CodeSandBox。

## 本地演示

这是重新布线的站点的局部外观。

<figure>

[https://www.youtube.com/embed/vAnMM5upae4](https://www.youtube.com/embed/vAnMM5upae4)

<figcaption>Ant Design CSS Working Locally</figcaption>

</figure>

你可以看到 babel-plugin-import 甚至在注释掉`import "antd/dist/antd.css"`之后还注入了必要的 CSS。

## CodeSandBox 演示

让我们在 CodeSandBox 上运行相同的代码(通过 [GitHub](https://github.com/dance2die/blog.dynamic_antd_css_import) 共享)。

<figure>[https://www.youtube.com/embed/VLN9OPwBRkg](https://www.youtube.com/embed/VLN9OPwBRkg)

<figcaption>蚂蚁设计 CSS 不在 CodeSandBox 上工作</figcaption>

</figure>

如果您注释掉 CSS 导入，CSS 将不再被导入。

所以让我们看看如何让 CSS 在本地工作&在 CodeSandBox 上。

## 让它在 CodeSandBox 上工作

诀窍是[通过检查 CSS 文件是运行在生产模式还是开发模式来动态导入](https://developers.google.com/web/updates/2017/11/dynamic-import)。