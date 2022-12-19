# create-nextron-app:在一个命令中用 Next.js app 创建一个电子

> 原文：<https://dev.to/saltyshiomix/create-nextron-app-create-an-electron-with-nextjs-app-in-one-command-4edn>

多亏了`npx`或`yarn`，你可以在一个命令中创建[next Ron](https://github.com/saltyshiomix/nextron)(Electron+next . js)应用程序:)

## TLDR

使用 [create-nextron-app](https://github.com/saltyshiomix/create-nextron-app) 。

```
# with npx
$ npx create-nextron-app test-app

# with yarn
$ yarn create nextron-app test-app 
```

## CLI 选项

通过使用`--example`选项，你可以指定一个例子来创建。

```
# with npx
$ npx create-nextron-app test-app --example with-typescript-material-ui

# with yarn
$ yarn create nextron-app test-app --example with-typescript-material-ui 
```

## 例子

此处列出了所有示例[。](https://github.com/saltyshiomix/nextron/tree/master/examples)

现在有 12 个例子:

```
- custom-build-options
- custom-server
- custom-server-nodemon
- custom-server-typescript
- ipc-communication
- parameterized-routing
- with-javascript
- with-javascript-ant-design
- with-javascript-material-ui
- with-typescript
- with-typescript-less
- with-typescript-material-ui 
```

## 欢迎 pr 和投稿

我是 OSS 新手，对 Nextron 有一些想法:

1.  展示了一种开发桌面应用程序的方法
2.  使用方便
3.  透明(对开放源码软件开发者开放)

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png)[saltyshiomix](https://github.com/saltyshiomix)/[next Ron](https://github.com/saltyshiomix/nextron)

### ⚡电子+ NEXT.js ⚡

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](../Images/99cef0fc5b1854bdbb76e71ac6195b81.png)T2】](https://camo.githubusercontent.com/25629b6d4d988b96bc78a552ac64cc79e5dfc2dc/68747470733a2f2f692e696d6775722e636f6d2f30766b78494d4e2e706e67)

[![NPM version](../Images/dcde8a40e5638d17f45a855d9aef1ead.png)](https://www.npmjs.com/package/nextron)[![NPM downloads](../Images/9e378b430cf1bf9cb40fb6e08bcdebc8.png)](https://www.npmjs.com/package/nextron)[![Package License (MIT)](../Images/e933c910c0aeadf4d6eee56f4b67616f.png)](https://camo.githubusercontent.com/eea0b734c9e12a63abc0b7c641af5fda6b6e129a/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6963656e73652f73616c74797368696f6d69782f6e657874726f6e2e7376673f7374796c653d666f722d7468652d6261646765266c6162656c436f6c6f723d303030303030)[![AWESOME NEXTJS](../Images/850d63ce57a0464b87dbdffb492273c1.png)T11】](https://github.com/unicodeveloper/awesome-nextjs)

## 支持

### Nextron vs Next.js

| 耐思创 | 然后 |
| --- | --- |
| `v5.x` | `v9.x` |
| `v4.x` | `v8.x` |
| `v2.x` / `v3.x` | `v7.x` |
| `v1.x` | `v6.x` |

```
👍 JavaScript frontend/backend
👍 TypeScript frontend/backend
👍 TypeScript frontend/Python backend 
```

### 包管理器

支持`npm`、`yarn`、`pnpm >= v4`。

## 我对 Nextron 的信念

1.  展示了一种开发桌面应用程序的方法
2.  使用方便
3.  对 OSS 开发者保持透明和开放

## 另有规定

*   如果你需要更多的电子性能，你应该看看这些样板文件
    *   [szwacz/电子样板](https://github.com/szwacz/electron-boilerplate)
    *   [chentsulin/电子反应样板](https://github.com/chentsulin/electron-react-boilerplate)
*   如果你想用 Nextron 做产品，请为你的行为负责
*   但是，如果你有任何麻烦，问题或想法，我会支持你，我保证

## 使用

### 安装

```
$ npm install --global nextron@latest
```

### 创建应用程序

要创建`<MY-APP>`，只需运行以下命令:

```
$ nextron init <MY-APP>
```

或者，您可以使用`create-nextron-app`命令，而不用全局安装`nextron`命令:

```
# with npx
$ npx create-nextron-app <MY-APP
```

…</article>

[View on GitHub](https://github.com/saltyshiomix/nextron)

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png)[saltyshiomix](https://github.com/saltyshiomix)/[create-nextron-app](https://github.com/saltyshiomix/create-nextron-app)

### 在一个命令中创建 nextron (Electron + NEXT.js)应用程序⚡

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](../Images/3b1dcb0f5e81470bcfb839014169df8b.png)T2】](https://camo.githubusercontent.com/3892e4b6b14152a865d75d180ded688a843266d9/68747470733a2f2f692e696d6775722e636f6d2f444e4f734148302e706e67)

[![](../Images/fcd5c08eecebe11e5754a9d4216ca9a9.png)](https://github.com/unicodeveloper/awesome-nextjs)[![](../Images/657b3ad1d470e40a2b7257f9456eb565.png)](https://www.npmjs.com/package/create-nextron-app)[![](../Images/05f978540a816de82effa3b01f73b139.png)](https://www.npmjs.com/package/create-nextron-app)

在一个命令中创建电子+ Next.js 应用程序 <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/26a1.png">⚡</g-emoji>

## 使用

### 使用 Npm

```
# `with-javascript` example
$ npx create-nextron-app my-app

# `with-typescript-material-ui` example
$ npx create-nextron-app my-app --example with-typescript-material-ui
```

### 用纱线

```
# `with-javascript` example
$ yarn create nextron-app my-app

# `with-typescript-material-ui` example
$ yarn create nextron-app my-app --example with-typescript-material-ui
```

## 例子

[https://github . com/saltyshiomix/nextron/tree/master/examples](https://github.com/saltyshiomix/nextron/tree/master/examples)

## 有关系的

[Nextron](https://github.com/saltyshiomix/nextron) - <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/26a1.png">⚡</g-emoji> 电子+ Next.js <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/26a1.png">⚡</g-emoji>

</article>

[View on GitHub](https://github.com/saltyshiomix/create-nextron-app)

## 结论

开发电子应用程序有时会面临一个问题，所以我希望`nextron`或`create-nextron-app`能帮助那些想用 Next.js 应用程序创建电子的人:)

感谢您阅读本文。

最好的，