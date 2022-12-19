# 使用 Parcel 引导 React TypeScript 项目

> 原文：<https://dev.to/learosema/bootstrapping-a-react-typescript-project-with-parcel-4oia>

使用[包](https://parceljs.org)捆绑器，您可以用(几乎)零配置引导一个 React 类型脚本项目。

首先，创建一个文件夹，放入 cd，初始化 NPM，安装包和你的 React 依赖项:

```
mkdir react-number-game
cd react-number-game
npm init -y
npm i parcel-bundler --save-dev
npm i react react-dom @types/react @types/react-dom --save
mkdir src 
```

Enter fullscreen mode Exit fullscreen mode

然后，打开您最喜欢的代码编辑器。在您的`src`目录中创建一个`index.html`文件。像 VSCode 这样的现代编辑器提供了 [Emmet](https://emmet.io) 完成特性。你可以只输入一个`!`，按 tab 键，你就得到一个基本的 html 结构。在主体内部，添加一个根 div 元素和一个脚本标记，引用您的条目`index.tsx`文件:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  React TypeScript App
</head>
<body>
  <div id="root"></div>
  <script src="./index.tsx"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

您的最小`index.tsx`文件可能如下所示。这里还没有特殊的类型脚本特性:

```
import * as React from 'react'
import { Component } from 'react'
import { render } from 'react-dom'
// import './index.css'

class App extends Component {
  render() {
    return (<h1>Hello World!</h1>)
  }
}

render(<App />, document.getElementById('root')) 
```

Enter fullscreen mode Exit fullscreen mode

最后，给你的`package.json` :
添加一个开始命令

```
{
  "name": "react-number-game",
  "version": "1.0.0",
  "description": "A number game in React",    
  "scripts": {
    "start": "parcel src/index.html",
  },
  "author": "Lea Rosema",
  "license": "MIT",
  "devDependencies": {
    // ...
  },
  "dependencies": {
    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以通过`npm start`启动您的应用程序。

### 附加项目配置

#### 生产构建

在您的`package.json`中添加一个构建命令，然后运行`npm run build` :

```
{
  "scripts": {
    "build": "parcel build src/index.html",
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 部署

如果您正在使用 GitHub，您可以使用`gh-pages` npm 包轻松地部署到 gh-pages。在构建
之前，我也使用`rimraf`包来清理 dist 文件夹

```
npm i rimraf gh-pages -D 
```

Enter fullscreen mode Exit fullscreen mode

将以下脚本添加到您的`package.json` :

```
{
  "scripts": {
    "build": "parcel build --public-url . src/index.html",
    "clean": "rimraf dist/index.html dist/src.*.css dist/src.*.js dist/src.*.map",
    "predeploy": "npm run clean -s && npm run build -s",
    "deploy": "gh-pages -d dist"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`build`步骤中的`--public-url .`参数很重要，因为您的项目部署在`https://username.github.io/projectname/`并且脚本默认包含一个斜杠(例如`/src.0123abcdef.js`)。这将导致 404 错误。

#### 打字稿

您可能需要额外的 TypeScript 配置。不过，这个最小的例子不需要任何配置就可以工作。您可以通过`node_modules/.bin/tsc --init`生成一个`tsconfig.json`。一个漂亮的最小的`tsconfig.json`可能是这样的:

```
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "strict": true,
    "jsx": "react"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 自动修复程序

通过`npm i autoprefixer -D`安装 autoprefixer 并增加一个`.postcssrc` :

```
{
  "plugins": {
    "autoprefixer": {
      "grid": true
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### SCSS

只需将一个`index.scss`文件添加到您的项目中，并将其导入到您的条目`index.tsx`中。package 会自动为您安装`node-sass`预编译器。

#### 。吉蒂尔

Parcel 在相关输出文件夹`dist`中构建 dist 文件，并且还有一个缓存文件夹`.cache`。我建议将它们添加到您的`.gitignore`文件:

```
dist/index.html
dist/src.*.js
dist/src.*.css
dist/src.*.map
.cache 
```

Enter fullscreen mode Exit fullscreen mode

#### 结果

请在 GitHub 上我的 [react-number-game](https://github.com/terabaud/react-number-game/) 库中查看结果代码。