# i18n 与 Lingui.js #1 反应

> 原文：<https://dev.to/stereobooster/i18n-of-react-with-linguijs-1-24oi>

每个人都在谈论 React 后的 React 钩子。其他的会谈没有得到那么多的关注。这很遗憾，因为有关于 React 应用程序的 i18n/l10n 的精彩演讲- [让 React 说你的语言，作者是 TomáEhrlich](https://www.youtube.com/watch?v=WXYPpY_mElQ&t=26701s)。

在这篇文章中，我想展示如何使用 Lingui.js 来做 React 应用程序的 i18n/l10n。我将使用 Node 10.10 和`yarn`，但我猜`npm`和其他版本的 Node 也可以。完整的源代码是[这里](https://github.com/stereobooster/react-lingui-example)。本教程的每一步都是作为单独的提交来完成的，因此您可以跟踪代码的所有更改。

## 安装

遵循 [Create React App](https://github.com/facebook/create-react-app) 文档获取更多信息。用下面的命令启动你的项目:

```
npx create-react-app react-lingui-example
cd react-lingui-example 
```

安装`@lingui/cli`、`@lingui/macro`和 Babel 核心包作为开发依赖，安装`@lingui/react`作为运行时依赖。

```
npm install --save-dev @lingui/cli@next @lingui/macro@next @babel/core babel-core@bridge
npm install --save @lingui/react@next

# or using Yarn
yarn add --dev @lingui/cli@next @lingui/macro@next @babel/core babel-core@bridge
yarn add @lingui/react@next 
```

在你的项目根目录下用 LinguiJS 配置创建`.linguirc`文件(在`package.json`旁边):

```
{
  "localeDir": "src/locales/",
  "srcPathDirs": ["src/"],
  "format": "lingui",
  "fallbackLocale": "en"
} 
```

该配置将从`src`目录中的源文件提取消息，并将其写入`src/locales`中的消息目录。

将以下脚本添加到您的`package.json` :

```
{
  "scripts": {
    "start": "lingui compile && react-scripts start",
    "build": "lingui compile && react-scripts build",
    "add-locale": "lingui add-locale",
    "extract": "lingui extract",
    "compile": "lingui compile"
  }
} 
```

使用您希望在应用程序中使用的[地区代码](https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry)运行`npm run add-locale`(或`yarn add-locale`):

```
npm run add-locale en

# or using Yarn
yarn add-locale en 
```

运行`npm run extract`(或`yarn extract`)检查安装情况:

```
npm run extract

# or using Yarn
yarn extract 
```

应该没有错误，您应该会看到类似如下的输出:

```
yarn extract
Catalog statistics:
┌──────────┬─────────────┬─────────┐
│ Language │ Total count │ Missing │
├──────────┼─────────────┼─────────┤
│ en       │      0      │    0    │
└──────────┴─────────────┴─────────┘

(use "lingui add-locale <language>" to add more locales)
(use "lingui extract" to update catalogs with new messages)
(use "lingui compile" to compile catalogs for production) 
```

恭喜你！您已经成功地用 LinguiJS 设置了项目。

## 基本用法

(基于[示例项目](https://github.com/lingui/js-lingui/tree/next/examples/react/src)

创建`src/i18n.js` :

```
import { setupI18n } from "@lingui/core";

export const locales = {
  en: "English",
  cs: "Česky"
};
export const defaultLocale = "en";

function loadCatalog(locale) {
  return import(/* webpackMode: "lazy", webpackChunkName: "i18n-[index]" */
  `./locales/${locale}/messages.js`);
}

export const i18n = setupI18n();
i18n.willActivate(loadCatalog); 
```

将`src/locales/*/*.js`加到`.gitignore`上。

在`App`上加上`<I18nProvider>`:

```
import { I18nProvider } from "@lingui/react";
import { i18n, defaultLocale } from "./i18n";

i18n.activate(defaultLocale);

class App extends Component {
  render() {
    return <I18nProvider i18n={i18n}>{/* ... */}</I18nProvider>;
  }
} 
```

使用`<Trans>`宏标记要翻译的文本:

```
import { Trans } from "@lingui/macro";

// ...

<Trans>Learn React</Trans>; 
```

运行`npm run extract`(或`yarn extract` ):

```
yarn extract
Catalog statistics:
┌──────────┬─────────────┬─────────┐
│ Language │ Total count │ Missing │
├──────────┼─────────────┼─────────┤
│ en       │      2      │    2    │
└──────────┴─────────────┴─────────┘ 
```

现在您可以用`npm run start`(或`yarn start`)启动您的开发环境。

您可以编辑`src/locales/*/messages.json`来更改翻译或将这些文件上传到翻译服务。