# 反应 js 满足开始

> 原文：<https://dev.to/tarekhassan410/react-js-contentful-starting-32h3>

Contentful 是一个 headless CMS，你可以用它的后端构建你的内容，并用 RESTFUL-API 获取你的内容。

在本教程中，我将与你分享如何在 Contentful 和 React js 上开始一个简单的项目。

本教程假设您已经熟悉 React 和创建 React 项目。

我将逐步解释创建和连接到 Contentful。

但首先你需要有一个满意的账户，只需去[https://www.contentful.com/sign-up/](https://www.contentful.com/sign-up/)注册然后登录。

*   在 Contentful 上创建内容的 4 个简单步骤

第一步:登录后，转到左侧的导航栏，选择创建一个新空间。这是您的数据将被保存的地方。在本教程中，我将这个空间命名为“博客”。

第二步:就是做内容类型。如果要为“文章”添加结构，请将内容类型设置为“文章”。在下一步添加内容时，您会发现“添加文章”按钮来添加内容。

让我们继续制作数据模型。

创建内容类型后，您可以找到“添加字段”。

添加字段->文本->短文本->制作标题'文章标题'
添加字段- >文本- >长文本- >制作标题'文章内容'
添加字段- >文本- >短文本- >制作标题'文章作者'

如果你在跟踪，你就会明白。您可以添加更多您想添加的字段。

在顶部导航栏上，单击“内容”。保存并转到内容。

第三步:添加内容要这么简单。如果您选择的内容类型是“文章”,只需点击“添加文章”。然后你就像在 WordPress 上一样添加内容。你可以使用 Lorem ipsum 生成器，如[http://lorem-ipsum.perbang.dk/](http://lorem-ipsum.perbang.dk/)来生成测试内容。

最后一步:获取你创建的内容。

您需要从空间设置-> API 密钥
中获取您的“访问令牌”和“空间 ID”

```
import React, { Component } from "react";
import { render } from "react-dom";
import { createClient } from "contentful";

var client = createClient({
  space: "YOUR_SPACE_ID",
  accessToken:
    "YOUR_ACCESS_TOKEN"
});

class App extends Component {
  constructor() {
    super();
    this.state = {
      article: []
    };
  }

  componentDidMount() {
    client.getEntries().then(({ items }) => {
      this.setState({ articles: items });
    });
  }

  render() {
    return (
      <div>
        Content goes here
      </div>
    );
  }
}

render(<App />, document.getElementById("root")); 
```