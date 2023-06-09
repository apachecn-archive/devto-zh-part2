# web3x-ether eums web 3 . js 的 TypeScript 端口，用于小型构建。

> 原文：<https://dev.to/xf00f/web3x---typescript-port-of-ethereums-web3js-for-tiny-builds-3jkj>

web3x 是 web3.js 到 TypeScript 的一个端口。除了对浏览器和 node.js 的干净、健壮、模块化、类型安全的客户端实现的需求之外，主要的驱动因素是构建规模。web3.js 的大小约为 800kb(压缩后约为 230kb)，当与应用程序可能需要的大量其他库结合使用时，它会对移动用户体验产生显著影响。每一点都有帮助。

web3x 的这个新版本着重于进一步减少构建规模。大多数 Dapps 只使用 Eth 模块，并期望最终用户使用像 MetaMask 这样的提供商来处理他们的帐户。在这种情况下，为多个提供者分发代码、在本地存储中处理帐户的加密代码、shh、bzz 等是没有意义的。通过适当地构造组件初始化，树抖动将排除大多数不需要的库。

使用 web3x 的最小应用程序通过 MetaMask 处理 ERC20 合同，将通过 webpack 生成大约 162kb(大约 46kb 压缩)的版本。

我致力于维护这个库，以满足 web3.js 的任何用户。此外，如果有任何开发人员发现 web3.js(和 web3x)缺少关键功能，这意味着他们必须导入其他库，请告诉我，因为 web3x 的目标是服务于尽可能多的用例。

Github:

[https://github.com/xf00f/web3x](https://github.com/xf00f/web3x)

包装:

[https://www.npmjs.com/package/web3x](https://www.npmjs.com/package/web3x)

[https://www.npmjs.com/package/web3x-es](https://www.npmjs.com/package/web3x-es)