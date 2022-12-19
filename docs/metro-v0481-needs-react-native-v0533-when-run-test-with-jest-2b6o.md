# 使用 jest 运行测试时，metro v0.48.1 需要 react-native v0.53.3

> 原文：<https://dev.to/sasurau4/metro-v0481-needs-react-native-v0533-when-run-test-with-jest-2b6o>

## TL；速度三角形定位法(dead reckoning)

如果您在升级 metro-react-native-babel-preset v 0 . 48 . 1 后遇到测试意外失败的问题，您可以尝试升级 react-native v0.57.3

## 玩笑失败的情况

有一天，我升级了很多库。升级后，我运行`yarn test`，看到 jest 失败，错误信息如下。

```
 TypeError: Cannot read property 'default' of undefined
      at new Icon (node_modules/react-native-vector-icons/lib/create-icon-set.js:42:389)
      ... 
```

对我的环境。

```
OS: Ubuntu 18.04
node: v11.0.0
react-native: v0.57.2
react-native-elements: v0.19.1
metro-react-native-babel-preset: v0.48.1
react-native-vector-icons: v6.0.2
@babel/runtime: v7.1.2 
```

my package.json

```
...
"jest": {
    "preset": "react-native",
    "transform": {
      "^.+\\.js$": "<rootDir>/node_modules/react-native/jest/preprocessor.js"
    }
... 
```

更多详情请看我的[重现库](https://github.com/sasurau4/jestBabelTransform/tree/jest-reproduce)
嗯，为什么 jest 测试失败了？

## jest 失败的原因

这是因为 metro 更改了 jest 时使用@babel/runtime 或不使用的默认配置。这个配置影响巴别塔是否使用插件转换运行时。

0.48.1 版本的 metro-react-native-babel-preset 默认使用 plugin-transform-runtime。
[https://github . com/Facebook/metro/pull/285/files # diff-691 dcb 134 bb2a 1276 f 7 a1 FEA 8728 f 634 l 150](https://github.com/facebook/metro/pull/285/files#diff-691dcb134bb2a1276f7a1fea8728f634L150)

但是 0.57.2 版的 preprocessor.js 只有 disableBabelRuntime 选项。
[https://github . com/Facebook/react-native/blob/v 0 . 57 . 2/jest/preprocessor . js # L55](https://github.com/facebook/react-native/blob/v0.57.2/jest/preprocessor.js#L55)

所以，我们的 javascript 代码是由 babel 通过插件-转换-运行时来转换的。

将 react-native 升级到 v0.57.3 解决问题。

## 详细问题:为什么 jest 用@babel/runtime 会失败？

我不明白与@babel/runtime 或 not 的区别。
我读了[https://babeljs.io/docs/en/babel-runtime](https://babeljs.io/docs/en/babel-runtime)。

对我来说是有意义的。但是仍然存在未知点。
我的情况中有`@babel/runtime`的依赖项，为什么属性‘default’为 undefined？

因此，我通过修改预处理器的代码来研究编译代码。

Jest 在`react-native-vector-icons package`的`create-icon-set.js`失败。

首先，我更改 preprocessor.js 的文件来截取生成的代码并将其写入 file。
[这里的](https://github.com/sasurau4/jestBabelTransform/tree/analyze-babel-and-jest-collaboration/intercepted)是生成的代码。
巴别塔生成的代码很小，不可读，所以回购代码的格式更漂亮。

两个文件的差异与巴别尔的文件所解释的相同。

我将 jest 中生成的代码和错误消息进行了匹配。
问题出在[babe-by-jest-with-runtime . js](https://github.com/sasurau4/jestBabelTransform/blob/analyze-babel-and-jest-collaboration/intercepted/babeled-by-jest-with-runtime.js#L82)的第 82 行

```
...
        (_getPrototypeOf2 = (0, _getPrototypeOf2.default)(Icon)).call.apply(
... 
```

该代码对应于 react-native-vector-icons 中的[静态属性类型](https://github.com/oblador/react-native-vector-icons/blob/master/lib/create-icon-set.js#L43)

第二，我通过`@babal\cli`传送`create-icon-set.js`。
这里的结果是

这里的结果是[这里的](https://github.com/sasurau4/jestBabelTransform/blob/analyze-babel-and-jest-collaboration/babeled/create-icon-set-enabled-runtime.js)

不同点的结果如下。

```
...
        (_getPrototypeOf2 = (0, _getPrototypeOf3.default)(Icon)).call.apply(
... 
```

jest 代码的 babel 是`_getPrototypeOf2.default`，cli 代码的 babel 是`_getPrototypeOf3.default`。
这种差异可能导致笑话失败。

我不相信，因为我不完全理解 jest 和 babel。

同样，metro 从提交开始使用 babel 运行时。

[从本期](https://github.com/facebook/react-native/pull/21283)开始，jest 仍然使用来自[的旧 regeneratorRuntime。](https://github.com/facebook/regenerator/tree/master/packages/regenerator-runtime)

我觉得影响了上面的结果。

分析的完整结果是[这里的](https://github.com/sasurau4/jestBabelTransform/tree/analyze-babel-and-jest-collaboration)

当你失败时，我很高兴这篇文章能帮助你。如果你发现了问题的原因或我的误解，请告诉我！
感谢您的阅读！