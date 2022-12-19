# i18n 与 Lingui.js #2 反应

> 原文：<https://dev.to/stereobooster/i18n-of-react-with-linguijs-2-25ec>

在这篇文章中，我想展示如何使用 Lingui.js 来做 React 应用程序的 i18n/l10n。我将使用 Node 10.10 和`yarn`，但我猜`npm`和其他版本的 Node 也可以。完整的源代码是[这里](https://github.com/stereobooster/react-lingui-example)。本教程的每一步都是作为单独的提交来完成的，因此您可以跟踪代码的所有更改。

## 高级用法

### 语言切换器

最简单的语言切换器可以这样实现:

```
import * as React from "react";
import { withI18n } from "@lingui/react";

const Navigation = ({ i18n, locales }) => (
  <select selected={i18n.locale}>
    {Object.keys(locales).map(locale => (
      <option key={locale} onClick={() => i18n.activate(locale)} value={locale}>
        {locales[locale]}
      </option>
    ))}
  </select> );

export default withI18n(Navigation); 
```

`withI18n`是一个 HOC，它提供了`i18n`对象。就是我们传给`<I18nProvider i18n={i18n}>`的那个。

`i18n.locale`提供当前区域设置。

`i18n.activate`更改当前区域设置。

### 翻译没有`Trans`宏的文本

```
import { I18n } from "@lingui/react";
import { t } from "@lingui/macro";

// ...

<I18n>
  {({ i18n }) => (
    <button onClick={() => alert(i18n._(t`You're looking good!`))}>
      <Trans>Show motto of the day</Trans>
    </button>
  )}
</I18n>; 
```

`I18n`是一个“渲染道具”组件，它提供了`i18n`对象。就是我们传给`<I18nProvider i18n={i18n}>`的那个。

`i18n._`是一个翻译函数，它将实际执行翻译。

`t`是由`extract`命令使用的宏。

要为翻译提供默认值，请使用注释:

```
i18n._(/* i18n: Default value for Hello {name} */ t`World`); 
```

### `Plural`

如果需要处理数字，使用`Plural`宏。

```
import { Plural } from "@lingui/macro";

// ...

<Plural
  value={count}
  _0="There're no books"
  one="There's one book"
  other="There're # books"
/>; 
```

### `DateFormat`

使用`DateFormat`来格式化日期。

```
import { DateFormat } from "@lingui/macro";

// ...

<Trans>
  Today is <DateFormat value={new Date()} /> </Trans>; 
```

### `NumberFormat`

使用`NumberFormat`格式化数字。

```
import { NumberFormat } from "@lingui/macro";

// ...

<NumberFormat value={2000} format={{ style: "currency", currency: "USD" }} /> <NumberFormat value={0.2} format={{ style: "percent" }} /> 
```