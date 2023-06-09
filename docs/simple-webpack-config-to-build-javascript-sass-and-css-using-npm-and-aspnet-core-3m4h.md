# 使用 ASP.NET 和 NPM 核心构建 javascript、sass 和 css 的简单 webpack 配置

> 原文：<https://dev.to/lukencode/simple-webpack-config-to-build-javascript-sass-and-css-using-npm-and-aspnet-core-3m4h>

我一直在做一个 ASP.NET 核心项目，需要我建立和发布 sass 样式表，css 和 javascript。我想简化这个过程，所以我组合了这个简单的 [webpack](https://webpack.js.org/) 设置来自动完成几个手动步骤。

Webpack 及其各种插件需要运行 Node.js 和安装 NPM。package.json 文件跟踪项目的节点依赖关系。我为 Webpack 使用的包是作为 devDependencies 安装的，可以使用带有-D 标志的 npm install 命令添加，例如:

```
npm install webpack -D 
```

Enter fullscreen mode Exit fullscreen mode

这是我的 package.json 文件:

当我们查看我的实际 webpack 配置时，我将解释我在这里包括的几个包。您可以使用 package.json 目录中的“npm install”来安装所有软件包。我发现这比包含额外的咕噜/咕噜脚本要简单得多。这些脚本是非常简单的构建和构建:dev 运行 webpack，用标志告诉它环境和它是否应该最小化输出。监视脚本在开发过程中非常有用，因为它会在检测到文件发生变化时自动重建资源。

这是 npm 正在运行的 webpack 配置脚本。它读取单个“入口”javascript 文件，并构建包含在指定输出路径中的任何 javascript、css 或 sass。

这项工作由模块部分中配置的加载程序来完成。Babel 是一款流行的 javascript 编译器，让你可以在网络上轻松使用 ES2015 和其他东西。

css 和 sass 规则使用“extract-text-webpack-plugin”将生成的 css 提取到一个单独的文件中(“site.css”)。Webpack 允许一些非常漂亮的设置，其中 css 是内联呈现的，或者与 react 和 vuejs 等 javascript 组件捆绑在一起，但出于我的目的，我将使用一个单独的 css 文件。

除了应用程序的任何 javascript 之外，条目文件将包括 javascript 导入或 require 依赖关系语句。这包括 sass 或 css 依赖项，尽管这个 webpack 脚本被配置为将它们导出到一个单独的文件。app.js 中的导入语句示例:

```
import '../css/theme.scss'
import '../vendor/tagsinput' 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，这些脚本完全独立于 ASP.NET 核心应用程序，并且作为 npm 脚本从控制台运行时运行良好:

```
npm run build
npm run watch 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将集成到中。net 构建和发布工作流，方法是在项目的构建过程中添加一些步骤。csproj 文件。这些脚本来自 webpack 上 [codeburst.io 的好帖子。](https://codeburst.io/how-to-use-webpack-in-asp-net-core-projects-a-basic-react-template-sample-25a3681a5fc2) 

```
<Target Name="DebugRunWebpack" BeforeTargets="Build" Condition=" '$(Configuration)' == 'Debug' And !Exists('wwwroot\dist') ">
    <!-- Ensure Node.js is installed -->
    <Exec Command="node --version" ContinueOnError="true">
      <Output TaskParameter="ExitCode" PropertyName="ErrorCode" />
    </Exec>
    <Error Condition="'$(ErrorCode)' != '0'" Text="Node.js is required to build and run this project. To continue, please install Node.js from https://nodejs.org/, and then restart your command prompt or IDE." />
    <!-- In development, the dist files won't exist on the first run or when cloning to a different machine, so rebuild them if not already present. -->
    <Message Importance="high" Text="Performing first-run Webpack build..." />
    <Exec Command="npm run build:dev" />
  </Target>

  <Target Name="PublishRunWebpack" AfterTargets="ComputeFilesToPublish">
    <Exec Command="npm install" />
    <Exec Command="npm run build" />    
    <ItemGroup>
      <DistFiles Include="wwwroot\dist\**" />
      <ResolvedFileToPublish Include="@(DistFiles->'%(FullPath)')" Exclude="@(ResolvedFileToPublish)">
        <RelativePath>%(DistFiles.Identity)</RelativePath>
        <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
      </ResolvedFileToPublish>
    </ItemGroup>
  </Target> 
```

Enter fullscreen mode Exit fullscreen mode

“DebugRunWebpack”目标将在开发模式下构建资产，如果它们还不存在的话。“PublishRunWebpack”将在运行 dotnet publish 时生成并包含这些文件。这对于自动生成管道(如 visual studio online)非常有用。

这里的设置非常简单，是一个很好的起点——尤其是如果您没有使用 react 这样的框架。可以根据项目添加的功能是将供应商资产分离到不同的文件/过程中，以及向 webpack 配置添加林挺、源地图等。