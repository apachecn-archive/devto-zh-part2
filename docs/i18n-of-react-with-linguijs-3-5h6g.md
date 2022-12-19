# i18n 与 Lingui.js #3 反应

> 原文：<https://dev.to/stereobooster/i18n-of-react-with-linguijs-3-5h6g>

在这篇文章中，我想展示如何使用 Lingui.js 来做 React 应用程序的 i18n/l10n。我将使用 Node 10.10 和`yarn`，但我猜`npm`和其他版本的 Node 也可以。完整的源代码是[这里](https://github.com/stereobooster/react-lingui-example)。本教程的每一步都是作为单独的提交来完成的，因此您可以跟踪代码的所有更改。

## 用法同 React 路由器

```
npm install --save react-router react-router-dom

# or using Yarn
yarn add react-router react-router-dom 
```

添加`BrowserRouter`、`Switch`、`Route`。

```
import { BrowserRouter, Switch, Route } from "react-router-dom";

// ...

<BrowserRouter>
  <Switch>
    <Route path="/" component={Home} exact />
  </Switch> </BrowserRouter>; 
```

添加`NotFound`页面。

```
<Switch>
  <Route path="/" component={Home} exact />
  <Route component={NotFound} /> </Switch> 
```

添加`I18nRoutes` :

```
const I18nRoutes = ({ match }) => { /* ... */ }

// ...

<Switch>
  <Route path="/:locale" component={I18nRoutes} />
  <Route component={NotFound} /> </Switch> 
```

它将从路径
中选择`locale`

```
const I18nRoutes = ({ match }) => {
  let { locale } = match.params;
  //...
}; 
```

它将检查是否支持`locale`，如果不支持，它将返回`NotFound`页面:

```
if (!supportedLocale(locale)) {
  i18n.activate(defaultLocale);
  return <NotFound />;
} 
```

支持`locale`激活给定`locale`并返回[嵌套路线](https://github.com/reactjs/react-router-tutorial/tree/master/lessons/04-nested-routes) :

```
i18n.activate(locale);
return (
  <Switch>
    <Route path={`${match.path}/`} component={Home} exact />
    <Route component={NotFound} />
  </Switch> ); 
```

### 从根路径重定向到本地化页面

根据浏览器首选项添加从根路径到本地化页面的重定向:

```
const RootRedirect = () => {
  let [locale] = (
    navigator.language ||
    navigator.browserLanguage ||
    defaultLocale
  ).split("-");
  if (!supportedLocale(locale)) locale = defaultLocale;
  return <Redirect to={`/${locale}`} />; };

// ...

<Switch>
  <Route path="/" component={RootRedirect} exact />
  <Route path="/:locale" component={I18nRoutes} /> </Switch>; 
```

### React 路由器的重新实现`LanguageSwitcher`

最简单的`LanguageSwitcher`可以用`NavLink` :
来实现

```
import { NavLink } from "react-router-dom";

export default ({ locales }) =>
  Object.keys(locales).map(locale => {
    const url = `/${locale}`;
    return (
      <NavLink
        key={locale}
        to={url}
        activeClassName={s.selected}
        className={s.link}
      >
        {locales[locale]}
      </NavLink>
    );
  }); 
```

该实现将总是重定向到本地化页面的根，例如从`/en/some/subpage`到`/cz`。

如果我们想改变语言环境，但保留路径，我们需要使用`Route` :

```
import { NavLink, Route } from "react-router-dom";

export default ({ locales }) => (
  <Route
    children={({ match }) =>
      Object.keys(locales).map(locale => {
        const url = match.url.replace(
          new RegExp(`^/${match.params.locale}`),
          `/${locale}`
        );
        // ...
      })
    }
  /> ); 
```

## 借助 React 头盔添加 meta 标签

```
npm install --save react-helmet

# or using Yarn
yarn add react-helmet 
```

该代码与我们在“React 路由器的重新实现`LanguageSwitcher`”中使用的代码非常相似:

```
import React from "react";
import { Route } from "react-router-dom";
import Helmet from "react-helmet";

export default ({ locales }) => (
  <Route
    children={({ match }) => (
      <Helmet
        htmlAttributes={{ lang: match.params.locale }}
        link={Object.keys(locales).map(locale => {
          const url = match.url.replace(
            new RegExp(`^/${match.params.locale}`),
            `/${locale}`
          );
          return { rel: "alternate", href: url, hreflang: locale };
        })}
      />
    )}
  /> ); 
```

将其添加到`Home`组件:

```
import Meta from "./Meta";

export default function Home() {
  return (
    <div style={{ padding: "20px 50px" }}>
      <Meta locales={locales} />
      {/* ... */}
    </div>
  );
} 
```