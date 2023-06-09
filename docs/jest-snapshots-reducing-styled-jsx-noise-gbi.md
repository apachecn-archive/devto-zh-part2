# Jest 快照:减少 styled-jsx 噪音

> 原文：<https://dev.to/kevinjalbert/jest-snapshots-reducing-styled-jsx-noise-gbi>

*封面图片[日全食光](https://flickr.com/photos/howardignatius/13875481115)由 [howardignatius](https://flickr.com/people/howardignatius) 授权[CC BY-NC-ND](https://creativecommons.org/licenses/by-nc-nd/2.0/)T7】*

脸书的 [Jest](https://facebook.github.io/jest/) 是一个强大的 JavaScript 测试框架。它在 React 项目中开箱即用，本质上是 React 事实上的测试框架。当我开始结合 React 使用 Jest 时，我爱上了[快照测试](https://facebook.github.io/jest/docs/en/snapshot-testing.html#snapshot-testing-with-jest)功能。根据主页的文档，快照有助于检测呈现的 DOM 中的结构退化:

> 捕获 React 树或其他可序列化值的快照，以简化测试并分析状态如何随时间变化。

在我使用 React 和 Jest 的过程中，我使用 [`styled-jsx`](https://github.com/zeit/styled-jsx) 作为我的 [CSS-in-JS](https://hackernoon.com/all-you-need-to-know-about-css-in-js-984a72d48ebc) 技术选择。很多次，我在做任何 CSS 修改的时候看到如下:

```
FAIL src/App.test.js
● renders without crashing

  expect(value).toMatchSnapshot()

  Received value does not match stored snapshot 1.

  - Snapshot
  1\. Received

  @@ -1,28 +1,23 @@
   <div
  - className="jsx-188895008 App"
  + className="jsx-3481390381 App"
   > 
```

Enter fullscreen mode Exit fullscreen mode

这是因为这个作用域组件的 CSS 发生了变化，因此`jsx-########`(唯一 id)反映了这一变化。

对我来说，快照差异中的这些变化是噪声，更难看到结构 DOM 的变化。DOM 元素的原始`className`仍然存在，理想情况下，我只想要没有任何`styled-jsx`的快照。

我们将首先使用 [create-react-app](https://github.com/facebook/create-react-app) 作为基础，从一个简化的`App`组件开始。目标是说明项目设置，快照看起来像什么，如何减少噪音，以及之后快照看起来像什么。`styled-jsx`提供了一种使用*内联样式*或*外部 CSS 文件*来样式化组件的方法，因此我们将在本文中同时考虑这两者。此外，我们还将考虑`react-test-renderer`和`enzyme` Jest 快照渲染方法。

鉴于上述信息，以下部分将涵盖这些场景:

*   带`react-test-renderer`的内嵌样式
*   带`enzyme`的内嵌样式
*   带`react-test-renderer`的外部样式
*   带`enzyme`的外部样式

## 内嵌样式

```
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div className="App">
        <p>
          Example Component
        </p>
        <style jsx>{`
          .App {
            text-align: center;
          }
        `}</style>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

为了让这一切工作，你必须在巴别塔配置中添加`styled-jsx/babel`到*插件*。

```
"babel": {
  "presets": [
    "react-app"
  ],
  "plugins": [
    "styled-jsx/babel"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 快照与反应-测试-渲染器

在内联样式的上下文中，我们首先来看看使用 [`react-test-renderer`](https://github.com/facebook/react/tree/master/packages/react-test-renderer) 测试 Jest 快照的默认方法。

```
import React from 'react';
import ReactDOM from 'react-dom';
import renderer from 'react-test-renderer';

import App from './App';

it('renders without crashing', () => {
  const tree = renderer.create(<App />).toJSON();
  expect(tree).toMatchSnapshot();
}); 
```

Enter fullscreen mode Exit fullscreen mode

这会生成以下快照:

```
exports[`renders without crashing 1`] = `
<div
  className="jsx-188096426 App"
>
  <p
    className="jsx-188096426"
  >
    Example Component
  </p>
</div>
`; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们改变 CSS 的一个方面(即`text-align`值)，我们得到下面的快照差异:

```
- Snapshot
+ Received

 <div
- className="jsx-188096426 App"
+ className="jsx-1500233327 App"
 >
   <p
- className="jsx-188096426"
+ className="jsx-1500233327"
   >
     Example Component
   </p>
 </div> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在 diff 中看到`jsx-########`噪声。这里要注意的另一件事是,`p`元素也有噪声，尽管我们的 CSS 并没有把它作为目标！

为了消除这种噪音，让我们从测试环境中移除`styled-jsx/babel`插件(您将想要指定您的不同环境):

```
"babel": {
  "presets": [
    "react-app"
  ],
  "env": {
    "production": {
      "plugins": [
        "styled-jsx/babel"
      ]
    },
    "development": {
      "plugins": [
        "styled-jsx/babel"
      ]
    },
    "test": {
      "plugins": [
      ]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你有了一个如下所示的快照:

```
exports[`renders without crashing 1`] = `
<div
  className="App"
>
  <p>
    Example Component
  </p>
  <style
    jsx={true}
  >

              .App {
                text-align: center;
              }

  </style>
</div>
`; 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，`jsx-########`值不再存在，尽管现在有了一个拥有实际 CSS 的`style`元素。在我看来，这是一个很好的交易——现在每个元素都没有千变万化的`jsx-########`。在我看来，仅此一点就能拍出更清晰的快照。

### 快照与酶

第二种方法是用 [`enzyme`](https://github.com/airbnb/enzyme) 进行快照测试。这个包为您提供了断言和操作组件输出的附加功能。不幸的是，呈现的组件被包装在一个`enzyme`特定的组件中，这产生了不必要的复杂快照。幸运的是， [`enzyme-to-json`](https://github.com/adriantoine/enzyme-to-json) 包提供了一种将包装的组件转换成我们熟悉的标准格式的方法。值得注意的是，如果你深入阅读`enzyme`和`enzyme-to-json`的文档，你可以进一步简化设置。

```
import React from 'react';
import ReactDOM from 'react-dom';
import Enzyme from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';
import { shallow } from 'enzyme';
import toJson from 'enzyme-to-json';

import App from './App';

Enzyme.configure({ adapter: new Adapter() });

it('renders without crashing', () => {
  const wrapper = shallow(<App />);
  expect(toJson(wrapper)).toMatchSnapshot();
}); 
```

Enter fullscreen mode Exit fullscreen mode

这会生成以下快照:

```
exports[`renders without crashing 1`] = `
<div
  className="jsx-188096426 App"
>
  <p
    className="jsx-188096426"
  >
    Example Component
  </p>
  <JSXStyle
    css=".App.jsx-188096426{text-align:center;}"
    styleId="188096426"
  />
</div>
`; 
```

Enter fullscreen mode Exit fullscreen mode

注意这里我们有一个额外的包含实际 CSS 样式的`JSXStyle`元素。这是在快照中原始噪声的之外的*。*

如果我们改变 CSS 的一个方面(即`text-align`值)，我们会得到下面的快照读数:

```
- Snapshot
+ Received

 <div
- className="jsx-188096426 App"
+ className="jsx-1500233327 App"
 >
   <p
- className="jsx-188096426"
+ className="jsx-1500233327"
   >
     Example Component
   </p>
   <JSXStyle
- css=".App.jsx-188096426{text-align:center;}"
- styleId="188096426"
+ css=".App.jsx-1500233327{text-align:left;}"
+ styleId="1500233327"
   />
 </div> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们用`react-test-renderer`应用与内联样式相同的修复(从测试环境中移除`styled-jsx/babel`插件)，我们现在得到相同的快照输出。因此，没有更多的`jsx-########`值，然而原始 CSS 在`style`标签内。

## 外部风格

我个人喜欢使用我导入组件的外部 CSS 文件。下面展示了我们转换后的`App`组件，它使用了一个导入的 CSS 文件，而不是内联样式:

```
import React, { Component } from 'react';
import css from './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <p>
          Example Component
        </p>
        <style jsx>{css}</style>
      </div>
    );
  }
}

export default App;

import css from 'styled-jsx/css';

export default css`
  .App {
    text-align: center;
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

### 快照与反应-测试-渲染器

使用外部 CSS 文件对*我们如何*测试组件没有影响。因此，我们可以使用内联样式部分中的相同测试。既然是这样，让我们采用同样的方法，通过从测试环境中移除`styled-jsx/babel`插件来消除 diff 中的噪声。

```
FAIL src/App.test.js
● Test suite failed to run

  styled-jsx/css: if you are getting this error it means that your `css` tagged template literals were not transpiled.

    at Object.<anonymous>.module.exports [as default] (node_modules/styled-jsx/css.js:2:9)
    at Object.<anonymous> (src/App.css.js:3:14)
    at Object.<anonymous> (src/App.js:2:12)
    at Object.<anonymous> (src/App.test.js:5:12) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们使用 [Jest 手册模仿](https://facebook.github.io/jest/docs/en/manual-mocks.html)来模仿`css`标记的模板文字，我们可以从这个错误中恢复。我们可以通过在`__mocks__ /styled-jsx/css.js` :
下创建以下模拟来实现这一点

```
function css() {
  return '';
}

module.exports = css; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的快照如下所示:

```
exports[`renders without crashing 1`] = `
<div
  className="App"
>
  <p>
    Example Component
  </p>
  <style
    jsx={true}
  />
</div>
`; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到,`jsx-########`值不再存在，此外,`style`标签没有原始 CSS。这是对内联风格方法的改进，因为快照不会随着任何 CSS 变化而变化。

### 快照与酶

我们可以使用与使用`react-test-renderer`测试内联样式时相同的测试。从我们现在所知道的来看，我们可以从测试环境中移除`styled-jsx/babel`插件，并模仿`css`标记的模板文字。这两个更改会产生与我们使用`react-test-renderer`在外部样式中收到的快照相同的快照。

考虑到在我编写的 React 测试中使用`enzyme`很常见，这是一个很好的结果，它提供了最干净的快照。

## TL；速度三角形定位法(dead reckoning)

*   如果您将`styled-jsx`用于 Jest 快照:
    *   每当 CSS 发生变化时，您都会看到`jsx-########`值的`className`变化
*   如果您使用内嵌样式:
    *   *从您的测试环境中移除*插件`styled-jsx/babel`
    *   参见使用`react-test-renderer`时清理快照
    *   使用`enzyme`时，查看干净快照(除了`style`标签下的原始 CSS)
*   如果使用外部样式:
    *   *从您的测试环境中移除*插件`styled-jsx/babel`
    *   *模仿*为`styled-jsx`标记的`css`模板文字
    *   参见使用`react-test-renderer`时清理快照
    *   参见使用`enzyme`时清理快照

可能有更好的方法来处理这个问题，但是在我写这篇文章的时候，我还没有看到一个干净的方法。我注意到的一件事是 [GitHub 的一个评论](https://github.com/zeit/styled-jsx/issues/117#issuecomment-342115323)提到了一个更好的方法，该方法将遵循与 [jest 风格组件](https://github.com/styled-components/jest-styled-components)相似的策略，但要不是`styled-jsx`。