# React，React Native 中的全局助手

> 原文：<https://dev.to/acro5piano/global-helper-in-react-native--32l4>

在任何项目中，我们都希望定义一些全局函数，这是很常见的情况。

没有全局函数，每次都要写`import`语句。

但这是否违背了 react 哲学？别担心，我在真实世界的项目中找到了下面的代码。

```
// make the i18n function "t" global so we don't have to import it in basically every file
import { t, jt } from "c-3po";
global.t = t;
global.jt = jt; 
```

Enter fullscreen mode Exit fullscreen mode

[https://github . com/metabase/metabase/blob/ed 83 df6 d 939 b 94 b 8193601 e 74676 EC 32 b 46 a 989 a/frontend/src/metabase/app . js # L10](https://github.com/metabase/metabase/blob/ed83df6d939b94b8193601e74676ec32b46a989a/frontend/src/metabase/app.js#L10)

我在当前的 React 本地项目中定义了一个全局函数`_` (lodash ),我没有任何问题。

# 如何

非常容易。

将此写入您的引导文件(例如`app.js` )

```
import _ from 'lodash'

global._ = _ 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以在任何地方使用`_`而不用显式地使用`import`。

```
<Text style={styles.title}>
  {_.truncate(someText, { length: 22 })}
</Text> 
```

Enter fullscreen mode Exit fullscreen mode

# 讲斯洛文尼亚语

ESLint 会将全局函数视为错误。因此，让我们将下面的配置添加到`.eslintrc` :

```
{
  //...
  "globals": {
    "_": false
  },
  //...
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，大量的全局函数可能会导致可读性、依赖性和可测试性问题。