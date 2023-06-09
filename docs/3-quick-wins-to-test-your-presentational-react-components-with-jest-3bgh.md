# 用 Jest 测试你的演示 React 组件的 3 个快速胜利

> 原文：<https://dev.to/a_reiterer/3-quick-wins-to-test-your-presentational-react-components-with-jest-3bgh>

你有没有问过自己，测试表现组件是否有意义，或者是否太费时间了？好消息，你并不孤单！这就是为什么我把三种方法放在一起，在不花费太多时间的情况下为你的表示组件创建有用的 Jest 测试。

> "测试只呈现用户界面的静态组件有意义吗？"

答案是:看情况。如果您只测试您的 JSX，或者如果您正确地调用了`componentDidMount`,它可能不会太有用。然而，在有些情况下，你真的想测试你的组件，以及一个或另一个速赢，可以帮助你避免一些讨厌的错误。

在这篇文章中，我们将讨论一些将基本测试引入你的应用的方法。请注意:这并不是一篇关于单元测试的秘密以及如何达到 100%代码覆盖率的全面深入的文章。然而，一些测试总比没有测试好——我们在接下来的章节中讨论的测试可以为您节省一些调试和修复 bug 的时间——而不会太复杂和耗时。

## 测试条件渲染

如果您的组件呈现不同的内容或子组件，这取决于您传递了哪个`props`,测试您的组件是否真的如预期的那样呈现是一个好主意。

为了用 Jest 断言你的渲染组件，你可以使用`enzyme`或者 React 的 [TestUtils](http://facebook.github.io/react/docs/test-utils.html) 。对于这个例子，我们使用`enzyme`,但是你可以随意使用最适合你的库。

```
import React from "react";
import { shallow } from "enzyme";
import ContactListWithLoadingIndicator from "./ContactListWithLoadingIndicator";
import LoadingIndicator from "./LoadingIndicator";
import ContactList from "./ContactList";

const dummyFunc = () => {};
const dummyArray = [
  { 
    id: 1, 
    firstName: "Max", 
    lastName: "Mustermann", 
    street: "Duck Creek Road", 
    house: 2561, 
    zip: 94107, 
    city: "San Francisco", 
    phone: "650-795-0470", 
    email: "max.mustermann@internet.com" 
  }, 
  { 
    id: 2,
    firstName: "Maxine", 
    lastName: "Musterfrau", 
    street: "Duck Creek Road", 
    house: 2562, zip: 94107, 
    city: "San Francisco", 
    phone: "650-795-0471", 
    email: "maxine.musterfrau@internet.com" 
  }
];

test("ContactListWithLoadInd shows LoadingIndicator when loading", () => { 
  const contactList = shallow(<ContactListWithLoadingIndicator isLoading={true} contacts={dummyArray} /> ); 
  const loadingIndicator = contactList.find(LoadingIndicator);

  expect(loadingIndicator).toHaveLength(1);
});

test("ContactListWithLoadInd shows ContactList when not loading", () => { 
  const contactList = shallow(<ContactListWithLoadingIndicator isLoading={false} contacts={dummyArray} />);
  const list = contactList.find(ContactList);

  expect(list).toHaveLength(1);}); 
```

在这个例子中，我们创建了两个单元测试。第一个，用`isLoading={true}`渲染我们的`<ContactListWithLoadingIndicator>`来检查它是否渲染了`<LoadingIndicator>`，而在第二个测试用例中，我们检查它是否渲染了当前没有加载的`<ContactList>`组件。

## 添加基本组件测试

> “它只是渲染了一些 UI，它不会坏，所以我不需要测试它”

想象一下下面的场景:在你创建你的应用程序几个月后，你收到一个变更请求，要求你在某一点上变更某个对象。其他组件可能会依赖于该对象，并且由于您的更改而中断。你不会知道它是否真的破坏了什么，直到你点击了你的整个应用程序。对于“整个应用程序”，我指的是我们的应用程序可能能够呈现的组件的每一种可能的组合。或者…你可以只希望没有其他东西依赖于你改变的对象。

听起来很有趣，是吧？

你可以避免点击应用程序所有可能的路径。为此，我们可以为您的每个组件添加基本组件测试。

为此，您必须为传递给组件的`props`的所有内容创建模拟对象。然后，您只需在 Jest 测试中使用 ReactDOM 呈现它，如果由于某种原因无法呈现，测试将会失败。

```
import React from "react";
import ReactDOM from "react-dom";
import ContactDetailView from "./ContactDetailView";

const dummyFunc = () => {};
const dummy = {
  id: 1,
  firstName: "Max",
  lastName: "Mustermann",
  street: "Duck Creek Road",
  house: 2561,
  zip: 94107,
  city: "San Francisco",
  phone: "650-795-0470",
  email: "max.mustermann@internet.com"
};

test("ContactDetailView rendered correctly", () => {
  const div = document.createElement("div");
  ReactDOM.render(
    <ContactDetailView
      contact={dummy}
      onDelete={dummyFunc}
      onEdit={dummyFunc}
    />,
    div
  );
}); 
```

“但是如果我在测试中总是通过一个正确的对象，我怎么会注意到我的应用程序从一个改变的对象中断了呢？”–你可能会想

你说得对。以上示例呈现了一个组件及其子组件。它只包括由组件或其子组件的更改引起的错误。然而，如果你改变了一个组件的属性，你也必须更新这个组件的测试。所以如果一些子组件通过`props`依赖于你改变的对象，这个测试就通不过，除非你修复了所有的子组件。如您所见，这个小测试可能会为您节省一些修复 bug 的时间。

## 添加基本 Jest 快照测试

快照测试是将呈现的标记与先前保存的快照进行精确比较的强大工具。

让我们看看如何为我们的`ContactDetailView`创建快照

```
import React from "react";
import ContactDetailView from "./ContactDetailView";
import renderer from "react-test-renderer";
const dummyFunc = () => {};
const dummy = {
   [... cropped for brevity ...]
};
test("ContactDetailView matches the Snapshot", () => {
  const component = renderer.create(
    <ContactDetailView
      contact={dummy}
      onDelete={dummyFunc}
      onEdit={dummyFunc}
    />
  );
  let tree = component.toJSON();
  expect(tree).toMatchSnapshot();
}); 
```

如您所见，我们首先使用`renderer.create`呈现组件。第一次运行将创建一个新的快照，该文件具有所呈现组件的确切标记。现在，每次我们执行测试时，它都会将呈现的标记与快照进行比较。

如果组件中的某些东西发生了变化，快照是进行非常详细的检查的好方法。这对于测试表示组件特别有用。

但是，有一些注意事项:每次您更改组件时，您都必须通过运行`jest -u`来覆盖现有的快照，从而生成一个新的快照。此时，有必要手动检查标记是如何改变的，以及它是否真的正确。您真的不希望您的测试运行在不正确的快照上。通常，您应该在提交文件之前检查快照。

**注意:**如果您不花时间手动检查快照文件中的更改，快照测试将毫无价值。

## 包装完毕

祝贺你的新技能！您刚刚学习了添加基本单元测试的三种方法，以测试 React 组件的表示性组件。

*   测试条件渲染
*   基本组件测试
*   基本快照测试

正如本文开头提到的，有很多方法可以详细测试你的组件。然而，如果你担心向你的应用程序添加测试所需要的时间，这三种方法是降低添加新错误风险的快捷方式。所以下次你考虑跳过测试以节省时间时，试试你最喜欢的方法，降低增加新错误的风险！

* *你写单元测试吗？为什么？为什么不呢？我很想听听！请留下评论，告诉我你的测试经历，以及你在项目中是如何处理这个主题的。**

* * *

你喜欢这个帖子吗？注册我的时事通讯，让更多类似的文章直接发送到你的收件箱。