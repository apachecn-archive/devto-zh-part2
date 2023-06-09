# 用 Typescript 和 Jest 进行 Jest Typescript 单元测试

> 原文：<https://dev.to/muhajirdev/unit-testing-with-typescript-and-jest-2gln>

[![vscode-jest](img/0a5978c265b88eb586fd12c7129fa857.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J6qf2Ctw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gd97g4kdyk1bpdeyfqst.png)

*原载于[https://muhajirframe.com/writing/unit-test-typescript-jest/](https://muhajirframe.com/writing/unit-test-typescript-jest/)T3】*

在本文中，我们将尝试在 Typescript + jest 中介绍一个简单的单元测试。

我们将创建一个简单的工具来检测一个 url 是内部链接还是外部链接。
例如`https://www.google.com`是外部链接，而`/page1`是内部链接。我们将把这个项目命名为`is-internal-link`，但是你可以给它取任何名字。

## 先决条件

*   NodeJS
*   VSCode + Jest 插件(可选)

创建新目录

```
mkdir is-internal-link 
```

初始化 npm

```
npm init 
```

安装依赖关系

```
npm install --save-dev @types/jest @types/node jest ts-jest typescript 
```

创建`jest.config.js`

```
module.exports = {
  roots: ['<rootDir>/src'],
  transform: {
    '^.+\\.tsx?$': 'ts-jest',
  },
  testRegex: '(/__tests__/.*|(\\.|/)(test|spec))\\.tsx?$',
  moduleFileExtensions: ['ts', 'tsx', 'js', 'jsx', 'json', 'node'],
} 
```

创建`tsconfig.json`

```
{  "compilerOptions":  {  "target":  "es5",  "module":  "commonjs",  "lib":  ["es2015"],  "strict":  true,  "declaration":  true,  "outDir":  "dist",  "sourceMap":  true  },  "include":  ["src/**/*"],  "exclude":  ["node_modules",  "**/*.spec.ts"]  } 
```

好了，我们准备写代码了。创建文件`src/main.ts`和`src/main.spec.ts`
你的文件树现在应该看起来像这样

```
.
├── node_modules
├── package-lock.json
├── package.json
├── src
│   ├── main.spec.ts
│   └── main.ts
└── tsconfig.json 
```

在您最喜欢的编辑器中打开它。(VSCode / Atom / Sublime / etc)。
我个人使用 VSCode

```
import { isInternalLink } from './main'

test('should return false given external link', () => {
  expect(isInternalLink('https://google.com')).toBe(false)
})

test('should return true given internal link', () => {
  expect(isInternalLink('/some-page')).toBe(true)
}) 
```

现在有一种方法来测试这一点。

### 方式 1

打开你的`package.json`。把你的`scripts`换成

```
 "scripts":  {  "test":  "jest"  }, 
```

运行`npm run test`。
现在你应该看到错误了，因为我们还没有实现代码，对吗？

### 方式二

用你的编辑器插件。我更喜欢这种方式。我只在 VSCode 上展示。你最喜欢的编辑可能也有。

安装〔t0〕vscode-jest〔t1〕

[![vscode-jest](img/bf86a4e2f5d0c1e353df3465626fa6a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a864oPzr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/jest-community/vscode-jest/raw/mastimg/vscode-jest.gif) 
这张 GIF，应该是自己对 vscode-jest 如何工作的一个很好的解释

我们继续。
[![vscode-jest](img/ccf44e5b7f81444cc9031d05000fc969.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xh_v6Awp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/38h9s3601vnvzl75wouf.png)

您的 VSCode 现在可能看起来像这样。

### 实现代码

让我们实现我们的`main.ts`

```
export const isInternalLink = (link: string) => /^\/(?!\/)/.test(link) 
```

切换回你的`main.spec.ts`。您应该看到错误已经消失，并且变成绿色。

亲提示:使用 [VSCode 拆分编辑器](https://code.visualstudio.com/docs/getstarted/userinterface#_side-by-side-editing)可以同时看到代码(`main.ts`)和规范(`main.spec.ts`)。

TL；速度三角形定位法(dead reckoning)

打开第一侧的`main.ts``cmd+1``cmd+p``main.ts`

打开第二面`cmd+2` `cmd+p1` `main.spec.ts`上的`main.spec.ts`

*附注:我是 VSCode 和 Vim 的忠实粉丝，我有很多关于如何使用 VSCode 提高您的生产力的技巧。如果你们感兴趣，请在评论中告诉我，我可以在这里写下*

恭喜你！

您刚刚使用 Typescript 和 Jest 进行了单元测试