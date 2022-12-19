# addon angular(1 . x)故事书

> -= ytet-伊甸园字幕组=-翻译:粒粒粒粒粒粒粒粒粒粒粒粒粒粒粒粒粒粒尘紫月皮皮 scenery 校对

几周前，我发布了包[storybook-addon-angularjs](https://www.npmjs.com/package/storybook-addon-angularjs)来帮助用 AngularJS 组件为 story book 创建故事。

特点:

*   简单语法
*   支持与其他插件集成，如`knobs`和`actions`

包含第一条公告和一个小屏幕截图的推文:

液体错误:内部

用 AngularJS 组件创建一个故事就像这样简单:

```
import { storiesOf } from "@storybook/html";

import { withKnobs, text, number } from "@storybook/addon-knobs";
import { action } from "@storybook/addon-actions";

import { forModule } from "storybook-addon-angularjs";

storiesOf("Components/Demo", module)
  .addDecorator(withKnobs)
  .add(
    "default",
    forModule("myApp").createElement(compile => {
      const name = text("Name", "Jane");

      const foo = {
        bar: number("Value", 20, { range: true, min: 0, max: 30, step: 1 })
      };

      const onEvt = action("clicked");

      return compile`<demo-component
                       name="${name}"
                       foo="${foo}"
                       on-ev="${onEvt}(num, name)"></demo-component>`;
    })
  ); 
```

Enter fullscreen mode Exit fullscreen mode

这取决于 Storybook 4+和 HTML 插件。

插件的代码:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[加-安-安-安-安-安-安-安-安-安-安-安-安-安-安-安-安-安-安](https://github.com/titonobre/storybook-addon-angularjs)

### 一个用 AngularJS 组件创建故事书故事的简单插件。

<article class="markdown-body entry-content container-lg" itemprop="text">

# AngularJS 故事书插件(1.x)

[![npm](img/ef94dc772a3225049240f455eedb986c.png)](https://www.npmjs.com/package/storybook-addon-angularjs)[![npm](img/8ca05c533637732a432737b5bcfc928a.png)](https://www.npmjs.com/package/storybook-addon-angularjs)[![GitHub issues](img/5d46dea77850a995f324364d45e4dec0.png)](https://github.com/titonobre/storybook-addon-angularjs/issues)[![GitHub](img/becb7c14a6ddbcfd65e66398bb9b1295.png)](https://github.com/titonobre/storybook-addon-angularjs/blob/master/LICENSE)[![Storybook](img/349ebe74e916d1467065053e94a0815f.png)](https://storybook.js.org/)

> **注意**这个插件打算和`@storybook/html`一起使用，从故事书 4 开始就有了。

## 装置

用你最喜欢的<g-emoji class="g-emoji" alias="package" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4e6.png">📦</g-emoji>包管理器在你的项目中安装插件的`devDependencies`:

**npm:**

```
npm install --save-dev storybook-addon-angularjs
```

Enter fullscreen mode Exit fullscreen mode

**纱线:**

```
yarn add --dev storybook-addon-angularjs
```

Enter fullscreen mode Exit fullscreen mode

## 使用

这个插件足够灵活，可以让你选择如何写故事。

你的故事可以像这样简单:

```
export default {
  title: "QuoteCard",
  decorators: [withAngularJs("myApp")],
};
export const simpleTemplate = () => `
 <quote-card author="'Me'">
 It works with a simple template!
 </quote-card>
`;
```

Enter fullscreen mode Exit fullscreen mode

但是你可以选择更高级的东西:

```
import { withKnobs, text } from "@storybook/addon-knobs";
import { action } from "@storybook/addon-actions";
import { html, withAngularJs } from "storybook-addon-angularjs";
class MockedAppService {
  constructor() {
    this.message =
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/titonobre/storybook-addon-angularjs)

一个工作实例:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[titon obre](https://github.com/titonobre)/[story book-addon-angularjs-example](https://github.com/titonobre/storybook-addon-angularjs-example)

### storybook-addon-angularjs 的工作示例。搬到:https://github.com/titonobre/storybook-addon-angularjs

<article class="markdown-body entry-content container-lg" itemprop="text">

# AngularJS (1.x)示例的故事书插件

> **注**
> 这个仓库被合并成了 [`storybook-addon-angularjs`](https://github.com/titonobre/storybook-addon-angularjs) 。

</article>

[View on GitHub](https://github.com/titonobre/storybook-addon-angularjs-example)

感谢反馈。
感谢阅读。