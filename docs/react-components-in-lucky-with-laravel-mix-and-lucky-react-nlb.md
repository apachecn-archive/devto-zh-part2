# 用 Laravel Mix 和 lucky-react 对 Lucky 中的成分进行反应

> 原文：<https://dev.to/mikeeus/react-components-in-lucky-with-laravel-mix-and-lucky-react-nlb>

经过 2 年的 Angular，我刚刚开始学习 React，我很惊讶 React 是如此有趣，社区和支持包是如此惊人。我也是 [Crystal](https://crystal-lang.org) 和 [Lucky framework](http://luckyframework.com) 的超级粉丝，那么还有什么比一起使用这些工具更棒的呢？

在这篇文章中，我将向你展示如何使用 Laravel mix 和 [lucky-react](https://github.com/mikeeus/lucky-react) 为你的幸运应用添加 React 组件。

## 演示

要查看这个特性的代码，您可以克隆`lucky_demo` repo 并检查`lucky-react`分支。

```
git clone git@github.com:mikeeus/lucky_demo.git
cd lucky_demo
bin/setup
git checkout lucky-react 
```

Enter fullscreen mode Exit fullscreen mode

否则你可以通过运行`lucky init app_name`跟随一个新的幸运应用。

## Laravel 混合

因为我们将使用 jsx 来编写 React 组件，所以我们需要更新 Laravel mix 的配置来正确编译我们的 JavaScript。Laravel mix 通过将配置中的`mix.js()`调用更改为`mix.react()`来简化设置。

```
// webpack.mix.js
mix
  // ...
  .react("src/js/app.js", "public/js") // instead of .js(...) 
```

Enter fullscreen mode Exit fullscreen mode

## 巴别塔插件:转换-类别-属性(可选)

为了使用箭头功能和其他令人敬畏的语法，我们需要 babel 的`transform-class-properties`插件。Laravel mix 默认没有这个插件，所以我们需要安装它。

`yarn add babel-plugin-transform-class-properties`

然后我们在项目的根目录下添加一个`.babelrc`文件，内容如下:

```
{  "plugins":  [  "transform-class-properties"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

这个会被 mix 自动拾取！毒品。

## 编写 React 组件

现在我们有了对 jsx 的支持，我们可以编写组件并将它们导入到我们的`app.js`文件中。对于 organization，我将组件放在了`src/js/components/`目录中。

这是演示应用程序中使用的`Bordered`组件和简化版本的`Chat`组件。

```
// src/js/components/Bordered.jsx
import React from 'react';

export class Bordered extends React.Component {  
  render() {
    return (
      <div style={{border: '2px solid'}}>
        {this.props.children}
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// src/js/components/Chat.jsx
import React from 'react';

import { ChatInput } from './ChatInput';
import { Message } from './Message';

export class Chat extends React.Component {
  ...
  render() {
    return (
      <div style={styles}>
        <h2 style={styles}>Conversation</h2>
        <div>
          {
            this.state.messages.map(message => 
              <Message
                key={message.id}
                sender={message.sender}
                text={message.text} />
            )
          }
        </div>
        <ChatInput writeMessage={this.onWriteMessage}/>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 幸运反应

为了允许在 Lucky 应用中渲染 React 组件，我创建了一个名为 [lucky-react](https://github.com/mikeeus/lucky-react) 的 npm 模块，它在`turbolinks:load`和`turbolinks:before-render`上添加事件监听器，以使用`[data-react-class]`和`[data-react-props]`属性来安装和卸载组件。

我还创建了一个名为 [lucky_react](https://github.com/mikeeus/lucky_react) 的水晶碎片，带有帮助器方法，用于在您的幸运页面中呈现具有这些属性的元素。

**lucky-react npm 模块**:使用`[data-react-class]`和`[data-react-props]`属性在你的页面上查找并渲染 react 组件。
**lucky_react 水晶模块**:增加了渲染具有正确属性元素的辅助方法，这样`lucky-react`就能找到它们。

现在让我们使用它们在主页上呈现我们的`Chat`和`Bordered`组件。

首先安装 npm 模块。

```
yarn add lucky-react 
```

Enter fullscreen mode Exit fullscreen mode

然后将碎片添加到`shard.yml`中，运行`shards`。

```
# shard.yml
...
  dependencies:
    ...
    lucky_react:
      github: mikeeus/lucky_react 
```

Enter fullscreen mode Exit fullscreen mode

## 注册反应组件

为了让`LuckyReact`呈现我们的组件，我们需要将它们导入并注册到我们的`app.js`文件中。

```
// src/js/app.js
...
import LuckyReact from "lucky-react";

import { Chat } from './components/Chat';
import { Bordered } from './components/Bordered';

LuckyReact.register({ Chat, Bordered }); 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们需要做的！如果在页面上找到这些组件，`LuckyReact`将在`turbolinks:load`和`turbolinks:before-render`上创建事件监听器来挂载和卸载它们。

注意，我们只需要注册`Chat`和`Bordered`，因为它们是仅有的根组件。`ChatInput`和`Message`嵌套在`Chat`中，将由 React 自动处理。

## 渲染页面上的组件

现在在我们的页面中，我们可以使用`LuckyReact` crystal 模块来添加引用我们的组件的元素，这样它们就可以被渲染了。

```
# src/pages/home/index_page.cr
require "lucky_react"

class Home::IndexPage < GuestLayout
  include LuckyReact # include the module

  def content
    react "Bordered" do # call react
      h1 "React Component", style: "text-align: center;"
    end

    messages = [
      { id: 1, sender: "me", text: "Hi" },
      { id: 2, sender: "Chatbot", text: "Hi! How can I help?" },
      { id: 3, sender: "me", text: "Can you tell me the time?" },
      { id: 4, sender: "Chatbot", text: "Sure it's #{Time.now}" }
    ]

    react "Chat", { messages: messages } # with props
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过添加`[data-react-class]`和`[data-react-props]`来渲染没有`lucky_react`碎片的组件。上面的例子应该写成这样:

```
# src/pages/home/index_page.cr
class Home::IndexPage < GuestLayout
  def content
    div  "data-react-class": "Bordered" do
      h1 "React Component", style: "text-align: center;"
    end

    messages = [
      { id: 1, sender: "me", text: "Hi" },
      { id: 2, sender: "Chatbot", text: "Hi! How can I help?" }
    ]

    div "data-react-class": "Chat", "data-react-props": ({ messages: messages }).to_json
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在运行应用程序并访问主页，您将看到聊天组件正在工作！

## 加入我们

我希望你喜欢这个教程，并发现它很有用。加入我们的 [Lucky gitter 频道](https://gitter.im/luckyframework/Lobby)了解框架的最新进展，或者[查看文档](https://luckyframework.org/guides)了解如何通过 Lucky 将您的应用理念付诸实践的更多信息。