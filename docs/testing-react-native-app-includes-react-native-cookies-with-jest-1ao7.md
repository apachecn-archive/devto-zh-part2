# 测试 React Native app 包括 react-native-cookies with Jest

> 原文：<https://dev.to/digitalsquad/testing-react-native-app-includes-react-native-cookies-with-jest-1ao7>

当出现以下错误时。

```
Test suite failed to run

    Invariant Violation: react-native-cookies: Add RNCookieManagerIOS.h and RNCookieManagerIOS.m to your Xcode project

      1 | import { Alert, AsyncStorage, FlatList, Platform, StyleSheet, Text, View } from 'react-native'
    > 2 | import CookieManager from 'react-native-cookies'
        |                                                              ^ 
```

Enter fullscreen mode Exit fullscreen mode

将 **setup.js** 添加到项目根目录的任意位置或某处。

```
jest.mock('react-native-cookies', () => {
  return {
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    openURL: jest.fn(),
    canOpenURL: jest.fn(),
    getInitialURL: jest.fn(),
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

修改 **package.json** 。

```
 "jest": {
    "preset": "react-native",
    "moduleDirectories": [
      "node_modules",
      "src",
      "test"
    ],
    "setupFiles": [
      "<rootDir>/setup.js"
    ]
  } 
```

Enter fullscreen mode Exit fullscreen mode

[“不变违例”运行测试时出现异常](https://github.com/joeferraro/react-native-cookies/issues/16#issuecomment-348448319)