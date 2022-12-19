# 修复运行 Jest 与 React native v0.56 的问题

> 原文：<https://dev.to/digitalsquad/fix-problems-on-running-jest-with-react-native-v056-2656>

Jest 和 React 本地版本 0.56 的组合可能会有一些问题，这些步骤应该可以解决这些问题。

```
yarn test
yarn run v1.7.0
$ jest
 FAIL  __tests__/timeline_form_screen_test.js
  ● Test suite failed to run

  Plugin 0 specified in "/ReactNative/AppName/node_modules/babel-preset-react-native/index.js" provided an invalid property of "default" (While processing preset: "/ReactNative/AppName/node_modules/babel-preset-react-native/index.js")

      at Plugin.init (node_modules/babel-core/lib/transformation/plugin.js:131:13)
      at Function.normalisePlugin (node_modules/babel-core/lib/transformation/file/options/option-manager.js:152:12)
      at node_modules/babel-core/lib/transformation/file/options/option-manager.js:184:30
          at Array.map (<anonymous>)
      at Function.normalisePlugins (node_modules/babel-core/lib/transformation/file/options/option-manager.js:158:20)
      at OptionManager.mergeOptions (node_modules/babel-core/lib/transformation/file/options/option-manager.js:234:36)
      at node_modules/babel-core/lib/transformation/file/options/option-manager.js:265:14
      at node_modules/babel-core/lib/transformation/file/options/option-manager.js:323:22
          at Array.map (<anonymous>)
      at OptionManager.resolvePresets (node_modules/babel-core/lib/transformation/file/options/option-manager.js:275:20) 
```

Enter fullscreen mode Exit fullscreen mode

如果你得到了上面的错误，重命名**。babelrc** 到 **.babel.config.js** 并修复错误。

然而仍然可能有另一个错误。

```
 /ReactNative/AppName/__tests__/component_test.js:1
    ({"Object.<anonymous>":function(module,exports,require,__dirname,__filename,global,jest){import React from 'react';
                                                                                                    ^^^^^

    SyntaxError: Unexpected identifier

      at ScriptTransformer._transformAndBuildScript (node_modules/jest-runtime/build/script_transformer.js:403:17) 
```

Enter fullscreen mode Exit fullscreen mode

如下图修改 package.json。

```
"jest": {
    "preset": "react-native"
  } 
```

Enter fullscreen mode Exit fullscreen mode

至

```
"jest": {
    "preset": "react-native",
    "transform": {
      "^.+\\.js$": "<rootDir>/node_modules/react-native/jest/preprocessor.js"
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

然后它会修复错误。