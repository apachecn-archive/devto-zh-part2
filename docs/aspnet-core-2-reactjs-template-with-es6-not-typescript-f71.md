# 带 ES6 的 ASP.NET 核心 2 React.js 模板(非类型脚本)

> 原文：<https://dev.to/dance2die/aspnet-core-2-reactjs-template-with-es6-not-typescript-f71>

我为`dotnet new`模板创建了一个 Nuget 包。

## 为什么？

我不知道为什么，但是 [dotnet CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x) 有 ASP.NET Core 2 react . js 的 SPA 模板，可以用 TypeScript 创建页面。

我从 C#背景中看到了 TypeScript 的许多好处。但有时这只是矫枉过正。
参考这些帖子(⚠️警告。长线程！)

*   如果 TypeScript 这么棒，为什么所有著名的 ReactJS 项目都使用 Babel？
*   [为什么 typescript 在 react 社区中没有被更多的使用？](https://www.reddit.com/r/reactjs/comments/7yxecm/why_isnt_typescript_used_more_in_the_react/)

作为 ASP.NET Core 的新人，我只是不想处理 TypeScript 所以我创建了一个新模板，[ASP.NET Core 用 React.js 用 ES6](https://www.nuget.org/packages/ReactES6.Web/) (简称`reactes6`)。

我将向您展示如何安装和运行 nuget 包。

## TL；速度三角形定位法(dead reckoning)

> dotnet new -i ReactES6。新网站。cd 新闻网站。Web
> dotnet 恢复& & npm 安装
> dotnet 运行
> 代码。

## 如何安装&运行

从命令行，

```
dotnet new -i ReactES6.Web 
```

你会看到一个名为`ASP.NET Core with React.js with ES6`(简称 *reactes6* )的新模板出现。

```
dance2die@CC C:\misc\sources\throwaway\coretemplates\fromnuget                                                                            
> dotnet new -i ReactES6.Web                                                                                                              
Getting ready...                                                                                                                          
  ...

Templates                                         Short Name       Language          Tags                                                 
--------------------------------------------------------------------------------------------------------                                  
Console Application                               console          [C#], F#, VB      Common/Console                                       
...      
ASP.NET Core with React.js with ES6               reactes6         [C#]              Web/MVC/SPA                                          
...                                  
ASP.NET Core with React.js                        react            [C#]              Web/MVC/SPA                                          
ASP.NET Core with React.js and Redux              reactredux       [C#]              Web/MVC/SPA                                          
...                          
MVC ViewStart                                     viewstart                          Web/ASP.NET 
```

使用模板`reactes6`创建新网站

```
dance2die@CC C:\misc\sources\throwaway\coretemplates\fromnuget
> dotnet new reactes6 -n NewSite.Web
The template "ASP.NET Core with React.js with ES6" was created successfully. 
```

转到新网站目录

```
cd NewSite.Web 
```

然后还原。NET 核心软件包和安装 NPM 软件包

```
dance2die@CC C:\misc\sources\throwaway\coretemplates\fromnuget\NewSite.Web
> dotnet restore && npm install Restoring packages for C:\misc\sources\throwaway\coretemplates\fromnuget\NewSite.Web\NewSite.Web.csproj...
  Restore completed in 140.27 ms for C:\misc\sources\throwaway\coretemplates\fromnuget\NewSite.Web\NewSite.Web.csproj.
  Generating MSBuild file C:\misc\sources\throwaway\coretemplates\fromnuget\NewSite.Web\obj\NewSite.Web.csproj.nuget.g.props.
  Generating MSBuild file C:\misc\sources\throwaway\coretemplates\fromnuget\NewSite.Web\obj\NewSite.Web.csproj.nuget.g.targets.
  Restore completed in 6.11 sec for C:\misc\sources\throwaway\coretemplates\fromnuget\NewSite.Web\NewSite.Web.csproj.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.1.3 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.1.3: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

added 611 packages from 525 contributors in 95.097s 
```

以`dotnet run`开始项目。

```
dance2die@CC C:\misc\sources\throwaway\coretemplates\fromnuget\NewSite.Web
> dotnet run
Using launch settings from C:\misc\sources\throwaway\coretemplates\fromnuget\NewSite.Web\Properties\launchSettings.json...
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\dance2die\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
Hosting environment: Development
Content root path: C:\misc\sources\throwaway\coretemplates\fromnuget\NewSite.Web
Now listening on: http://localhost:60672
Application started. Press Ctrl+C to shut down. 
```

打开你选择的编辑器(下面我用的是 VS 代码)。像个老板一样和 HMR 玩😎。

[![HMR demo](../Images/0e387ffcac39d98941a06b9b97486afb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NFfrGPCQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/Rbo51jW.gif)

### 源代码

GitHub ( [dance2die/ReactES6)上有。Web](https://github.com/dance2die/ReactES6.Web) )拥有麻省理工学院许可证。