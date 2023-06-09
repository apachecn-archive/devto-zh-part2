# 可伸缩 React 组件架构

> 原文：<https://dev.to/carloscuesta/scalable-react-components-architecture-3k2e>

#### *原载于 [carloscuesta 的博客](https://carloscuesta.me/blog/scalable-react-components-architecture)*

我开始用 [**React**](http://reactjs.org) 和 [**React-Native**](https://facebook.github.io/react-native/) 制作已经有一段时间了。关于 **React** 的**最伟大的事情之一**是库给你的**灵活性**。这意味着你可以自由决定如何实现项目的几乎每个细节，例如*架构和结构*。

然而，从长远来看，这种自由可能会导致一个复杂而混乱的代码库，特别是如果你不遵循某种模式的话。在这篇文章中，我将解释**一个简单的方法**来**组织**和**结构**反应**组件**。

> 一个组件是一个 **JavaScript** **函数**或者是**返回**一个**UI**的类。

我们将创建一个`EmojiList`组件，然后我们将重构它，应用**文件夹模式**将它分解成更小的独立部分。我们的组件看起来是这样的:

[![emojilist](img/6be4e3ce7a4af579c72415b86ddaa3e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JRJC53zF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/carloscuesta/image/upload/emojilist-compressed.png)

### 表情列表

正如我之前提到的，我们可以从非常简单和小的地方开始，不需要遵循任何模式。这是我们包含在单个函数中的`EmojiList`组件。

[在 CodeSandbox 上打开演示](https://codesandbox.io/s/3vjxn0ykyq)

如果你打开 *CodeSandbox 侧边栏*，你会看到我们的文件树是这样的:

```
.
├── components
│   ├── EmojiList.js
│   └── styles.js
└── index.js 
```

Enter fullscreen mode Exit fullscreen mode

这种方法没有任何问题。但是在**更大的代码库**上，那种组件**就变得很难维护**，因为里面有很多东西:*状态*， *ui* ，*数据*...看看下面我们的组件代码👇

`EmojiList.js`

```
import React from "react"

import styles from "./styles"

class EmojiList extends React.Component {
  state = {
    searchInput: "",
    emojis: []
  }

  render() {
    const emojis = this.state.emojis.filter(emoji =>
      emoji.code.includes(this.state.searchInput.toLowerCase())
    )

    return (
      <ul style={styles.list}>
        <input
          style={styles.searchInput}
          placeholder="Search by name"
          type="text"
          value={this.state.searchInput}
          onChange={event => this.setState({ searchInput: event.target.value })}
        />
        {emojis.map((emoji, index) => (
          <li key={index} style={styles.item}>
            <div style={styles.icon}>{emoji.emoji}</div>
            <div style={styles.content}>
              <code style={styles.code}>{emoji.code}</code>
              <p style={styles.description}>{emoji.description}</p>
            </div>
          </li>
        ))}
      </ul>
    )
  }
}

export default EmojiList 
```

Enter fullscreen mode Exit fullscreen mode

改进这段代码的一个步骤是在同一个文件中创建单独的组件，然后在主组件中使用它们。然而，你将在其他事情中分享风格，这可能会令人困惑。

#### 重构

让我们开始将单个组件重构为多个组件，方法是[将 UI 分解成一个组件层次结构](https://reactjs.org/docs/thinking-in-react.html#step-1-break-the-ui-into-a-component-hierarchy)。

[![emojilist-breakdown](img/b64cc0c61847ab8cbe571d18cc151b71.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T2t_wBVk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/carloscuesta/image/upload/emojilist-breakdown.png)

如果我们看一下图片，很容易识别出我们可以**将**分解成**三个**不同的**组件** : 🛠中的 **UI**

*   **`EmojiList`** :组合较小的组件，向下共享状态。
    *   **`SearchInput`** :接收用户输入，显示搜索栏。
    *   **`EmojiListItem`** :显示每个表情符号的列表项，带有*图标*、*名称*和*描述*。

我们将**为**的每个** **组件**创建**一个**文件夹**，有两个文件，一个`index.js`将保存组件和`styles.js`的所有代码。这是这种模式的好处之一。每个组件都定义了自己的 UI 和风格，**将**这段**代码与**另一个**组件**隔离开来**不需要知道它们的任何事情**。

[在 CodeSandbox 上打开演示](https://codesandbox.io/s/lx8ykrljl9)

注意，在`EmojiList`文件夹中，(*是一个组件*，我们添加了两个嵌套组件，它们将只在`EmojiList`组件中使用。再说一遍，这是因为这两个组件不会在那个上下文之外使用。这有助于减少视觉混乱。

```
.
├── EmojiList
│   ├── EmojiListItem
│   │   ├── index.js
│   │   └── styles.js
│   ├── SearchInput
│   │   ├── index.js
│   │   └── styles.js
│   ├── index.js
│   └── styles.js
└── index.js 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们将代码从最小到最大分离成三个部分:

`EmojiListItem/`

该组件呈现将出现在列表中的每个表情符号项目。

```
import React from "react"

import styles from "./styles"

const EmojiListItem = (props) => (
  <li style={styles.item}>
    <div style={styles.icon}>{props.emoji}</div>
    <div style={styles.content}>
      <code style={styles.code}>{props.code}</code>
      <p style={styles.description}>{props.description}</p>
    </div>
  </li>
)

export default EmojiListItem 
```

Enter fullscreen mode Exit fullscreen mode

`SearchInput/`

该组件接收用户输入并更新父组件的状态。

```
import React from "react"

import styles from "./styles"

const SearchInput = (props) => (
  <input
    style={styles.searchInput}
    placeholder="Search by name"
    type="text"
    value={props.value}
    onChange={props.onChange}
  />
)

export default SearchInput 
```

Enter fullscreen mode Exit fullscreen mode

`EmojiList/`

这是顶层组件，保存示例的状态和数据，并导入其他组件来重新创建这个小应用程序的整个 UI。隔离组件使得渲染方法更具可读性，也更容易理解✨.

```
import React from "react"

import SearchInput from "./SearchInput"
import EmojiListItem from "./EmojiListItem"
import styles from "./styles"

class EmojiList extends React.Component {
  state = {
    searchInput: "",
    emojis: []
  }

  render() {
    const emojis = this.state.emojis.filter(emoji =>
      emoji.code.includes(this.state.searchInput.toLowerCase())
    )

    return (
      <ul style={styles.list}>
        <SearchInput
          onChange={(event) => this.setState({ searchInput: event.target.value })}
          value={this.state.searchInput}
        />
        {emojis.map((emoji, index) => (
          <EmojiListItem
            key={index}
            code={emoji.code}
            description={emoji.description}
            emoji={emoji.emoji}
          />
        ))}
      </ul>
    )
  }
}

export default EmojiList 
```

Enter fullscreen mode Exit fullscreen mode

这基本上是我在工作的公司使用的架构。我对使用这种模式的**体验**非常满意。我们的组件变得更容易维护和使用。无论如何，软件工程上没有**银弹**，所以**想想什么对你**或者你的团队最有效！