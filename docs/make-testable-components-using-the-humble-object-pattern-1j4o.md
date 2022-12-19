# 使用简单对象模式制作可测试的组件

> 原文：<https://dev.to/krofdrakula/make-testable-components-using-the-humble-object-pattern-1j4o>

*封面图片由[克里斯·麦克法兰](https://www.flickr.com/photos/kd4pwh/5980242616/in/photolist-a7siMj-8iy9Dy-cL2dfU-RQkA4V-M1h2U-8JhXxe-4BW1Up-8Jm2cu-smydH1-K9uC4x-9BgZG3-o1krfr-8Jm1sQ-QMVMw-964Q1j-nuC3Yd-7RbxxJ-76kXXz-aFwuSM-69kvVh-5t7W85-f9Bg72-ft4BUx-82x3VC-66U3ou-pZzUJa-jr85aj-28WAhYG-eZnhTT-fdiVvG-aXo2uc-c1pVSJ-2hYqJ3-jKESwZ-dVPC9K-7xLhi-fXUGNa-L6sL7-4zEAcX-ScKVPr-6Nb7p5-NwevL1-39gBGe-5NrcZW-2VztZa-aq4DDi-bPbsWi-qSRbyK-9G76Gn-njYKmS)提供。*

如果你曾经接触过 [React](https://reactjs.org/) 或者它的兄弟姐妹( [Preact](https://preactjs.com/) 和 [Inferno](https://www.infernojs.org/) )，你很可能在试图找出如何测试组件的行为时碰壁了。

你可能会想，*“没关系，我可以用 Jest、JSDOM 和 Enzyme 来解决问题，或者用 Puppeteer 或 Karma 在真正的浏览器中运行测试！”*

如果您不担心在 CI 环境中设置所有这些工具，那么您可能还没有处理过这些工具的设置问题，尤其是如果您碰巧偏离了常规。

如果你和我一样，你希望你的测试很快，并且在没有 HTML 页面的情况下运行。

如果您阅读本文是为了了解如何为您的组件设置浏览器测试，那么您将会大失所望。我在这里写的测试都可以运行 vanilla node，没有服务器端 DOM 伪造！

不过，从好的方面来看，您将有望发现一种不同的方法来分离组件的可测试部分，而不必构建一个假的(或真实的)浏览器！

拉把椅子，拿上你最喜欢的热饮，我们来谈谈...

## ...卑微的对象模式

在鲍勃叔叔的书[清洁架构](https://www.goodreads.com/book/show/18043011-clean-architecture)中，我们亲爱的老叔叔谈到让我们应用程序中痛苦的部分做最少的工作，并把我们重要的逻辑集中在独立的、不可知的代码中。

他谈到了不起眼的物体，简单明了到几乎透明的东西，以至于测试起来似乎很愚蠢。这就是我们的视图对象应该看起来的样子。这是测试起来很痛苦的部分，我们希望它尽可能简单。

另一方面，我们有呈现器，它向不起眼的对象提供数据，并处理任何需要完成的繁重工作，以便呈现视图。

好吧，听起来很有道理，但是*如何*还是有点朦胧。让我们更具体一点。

假设您正在编写一个组件，该组件应该呈现一个项目列表(带有链接)，由下面的示例数据描述:

```
const PHOTO_LIBRARY = [
  { id: 1, url: '...', title: '...', description: '...' },
  // ...
]; 
```

Enter fullscreen mode Exit fullscreen mode

这听起来很简单。让我们编码吧，PFC 风格！如果您不熟悉首字母缩写词， **PFC** 代表纯功能组件，也称为无状态组件。实际上，任何采用`props`并返回`JSX`的函数都被认为是无状态组件或 PFC。

```
/**@jsx h*/
import { h } from 'preact';            // cuz that's how I roll these days
import styles from './photo_list.css'; // I'm a sucker for CSS modules

const PhotoList = ({ photos = [] }) => (
  <ul className={styles.list}>
    {photos.map(photo => (
      <li className={styles.item}>
        <a href={photo.url}>
          <img src={photo.url} alt={photo.description}/>
        </a>
      </li>
    ))}
  </ul>
);

export default PhotoList; 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但理想情况下，嵌套应该让你有点畏缩。到目前为止，解析起来并不困难，但是将项目提取到它自己的组件中可能会很有用。

```
export const Photo = ({ url, description, title }) => (
  <li className={styles.item}>
    <a href={url}>
      <img src={url} alt={description} title={title} />
    </a>
  </li>
);

const PhotoList = ({ photos = [] }) => (
  <ul className={styles.list}>
    {photos.map(Photo)}
  </ul>
); 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，即使只是看一眼，我们也很有信心这段代码应该可以工作。让我们更进一步，当列表为空时显示一条消息。

```
const PhotoList = ({ photos = [] }) => {
  if (photos.length == 0) {
    return (
      <div className={styles.empty}>🤔 Wut.</div>
    );
  } else {
    return (
      <ul className={styles.list}>
        {photos.map(Photo)}
      </ul>
    );
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

此时，你的右脸颊上部可能会出现轻微的抽搐(或者只有我会这样？).你可以把这个表达式转换成一个三元组来去掉花括号，但是这只会混淆更明显的真正处于问题核心的`if`语句。

虽然看起来我似乎想多了一些显而易见的东西，但是请考虑一下您或任何其他开发人员将来会如何处理这样一个组件。仅仅添加另一个`else if`扩展名，或者仅仅嵌套一个表达式不是更容易吗？你允许多少嵌套？什么时候才算真正够了？

更不用说我甚至还没有考虑写一个测试！

## 进入演示者，舞台左侧

让我们将顶部的组件分成两部分:一部分决定呈现哪个视图以及每个视图需要的数据，其余的只是简单的组件。

此外，现在是展示高阶组件(通常缩写为 *HOC* )如何真正有助于让我们的生活更轻松的好时机。让我们编写一个特设来连接一个演示者和一个组件。

```
const DEFAULT_PRESENTER = props => props;

export const withPresenter = (Inner, presenter = DEFAULT_PRESENTER) =>
  (props, context) =>
    <Inner {...presenter(props, context)} />; 
```

Enter fullscreen mode Exit fullscreen mode

让我们来分解一下`withPresenter`是做什么的:

1.  它接受一个组件和一个 presenter 函数，并返回一个纯函数组件(一个返回 JSX 的函数)。
2.  该组件将其`props`和`context`传递给 presenter 函数，该函数返回一组新的`props`。
3.  使用从演示者返回的道具来呈现`Inner`组件。

默认的表示器只是原样返回`props`，所以包装一个没有表示器的组件没有任何作用。我们用这个把处理道具的逻辑提取到自己的函数里。

```
export const PhotoList = ({ photos }) => (
  <ul className={styles.list}>
    {photos.map(Photo)}
  </ul>
);

export const EmptyList = () => (
  <div className={styles.empty}>🤔 Wut.</div>
);

export const PhotoListTemplate = ({ photos, isListEmpty, Empty, List }) => (
  isListEmpty ? <Empty/> : <List photos={photos} />
);

export const photoListPresenter = ({ photos = [] }) => ({
  photos,
  isListEmpty: photos.length == 0,
  Empty: EmptyList,
  List: PhotoList
});

export default withPresenter(PhotoListTemplate, photoListPresenter); 
```

Enter fullscreen mode Exit fullscreen mode

首先，您会注意到，我们为这个小示例导出了大量组件，而且理由充分。

UI 变化很大。您不希望通过断言诸如显式样式断言或 HTML 中字符串的文本匹配之类的事情来降低自己的速度。你想要避免测试那些受你的设计团队支配的当前模式的东西。

现在，我并不是说您应该只编写组件并进行部署。我强烈建议你发布一个包含最新代码的实时样式指南，使用类似于 [Storybook](https://storybook.js.org/) 的东西来展示 UI 的各个部分。使其作为每次提交的一部分发布。让团队中的每个人都可以访问它，尤其是那些提出变更的人，这样他们就可以验证组件本身的外观和感觉。

而且，让我们面对现实吧，只要涉及到 UI，100%的测试覆盖率就是一个白日梦。一些眼球测试是无法避免的。

所以我们来谈谈`withPresenter`这一点。

我们创建了一个演示者，它返回以下属性:

1.  `photos`:原始数据的数组
2.  `isListEmpty`:一个做它所说的事情的布尔值
3.  `Empty`:列表为空时要渲染的组件
4.  `List`:有照片显示时要渲染的组件

这现在被绑定到`PhotoListTemplate`，然后根据`isListEmpty`标志，它呈现`Empty`或`List`。

我们现在可以用不同的道具组合无条件地渲染每个组件，而不用担心任何逻辑！

嗯，差不多了。在`PhotoListTemplate`中仍然有三进制表达式。

## 等级提升:`makeChoice()`

这里有一个在纯功能组件中去掉`if-else`构造的好方法:

```
export const makeChoice = (predicate, True, False) =>
  (props, context) =>
    predicate(props, context) ? <True {...props}/>: <False {...props}/>; 
```

Enter fullscreen mode Exit fullscreen mode

你能猜到这是干什么的吗？

没错。你猜对了。如果`predicate`在通过`props`时返回`true`，那么`True`返回什么，它就返回什么，反之亦然。

让我们牢记这一点来重写我们的模板组件:

```
export const PhotoListTemplate = makeChoice(
  props => props.isEmptyList,
  ({ Empty }) => <Empty/>,
  ({ List, photos }) => <List photos={photos} />
); 
```

Enter fullscreen mode Exit fullscreen mode

这看起来可能有点奇怪，但是让我们来看看`makeChoice`的三个参数是什么:

1.  `predicate`是当来自`props`的`isEmptyList`为`true`时返回 true 的函数。
2.  当`true`时，我们从`props`中取出`Empty`组件并渲染。
3.  当`false`时，我们通过将`photos`传递给它来呈现`List`。

塔-达！现在，您已经成功地从演示组件中移除了所有逻辑。所有的视图组件都是完全声明性的，没有逻辑。

现在让我们看看如何测试我们的演示者和模板。

## 测试演示者和模板

由于 presenter 只是一个接受`props`并返回`props`的函数，我们可以为它创建几个测试:

```
// we're using Jest with Jasmine syntax here, but feel free
// to use any test framework you like, or just use `console.assert`!

import { expect } from 'chai'; // kill your darlings!

import { photoListPresenter } from './photo_list';

describe(photoListPresenter, () => {

  it('should correctly determine an empty list', () => {
    const photos = [];

    expect(photoListPresenter({ photos }))
      .to.have.property('isEmptyList', true);
  });

  it('should correctly determine a populated list', () => {
    const photos = [{ id: 1 }];

    expect(photoListPresenter({ photos }))
      .to.have.property('isEmptyList', false);
  });

}); 
```

Enter fullscreen mode Exit fullscreen mode

让我们也为模板函数添加测试，我们必须使用我们的视图库(在本例中，使用`preact-render-to-string` ):
来呈现它

```
/**@jsx h */
// this render function doesn't require a DOM
import render from 'preact-render-to-string';
import { h } from 'preact';
import { expect } from 'chai';

import { PhotoListTemplate} from './photo_list';

describe(PhotoListTemplate, () => {

  it('should render an empty list when there are no photos to show', () => {
    const photos = [];
    const Empty = jest.fn(() => null); // we make a fake component to see
    const List = jest.fn(() => null);  // which one of these is rendered

    render(
      <PhotoListTemplate
        photos={photos}
        isEmptyList={true}
        Empty={Empty}
        List={List}
      />
    );

    expect(Empty.mock.calls.length).to.equal(1); // was Empty rendered?
    expect(List.mock.calls.length).to.equal(0); // was List not rendered?
  });

  it('should render a populated list when there are photos to show', () => {
    const photos = [{ id: 1 }];
    const Empty = jest.fn(() => null);
    const List = jest.fn(() => null);
    render(
      <PhotoListTemplate
        photos={photos}
        isEmptyList={false}
        Empty={Empty}
        List={List}
      />
    );

    expect(Empty.mock.calls.length).to.equal(0); // was Empty not rendered?
    expect(List.mock.calls.length).to.equal(1); // was List rendered?
    expect(List.mock.calls[0][0]).to.eql({ photos }); // was List given photos?
  });

}); 
```

Enter fullscreen mode Exit fullscreen mode

这个测试基本上结束了渲染代码中以前包含的任何逻辑的循环。当然，您也可以测试一下我的`withPresenter`和`makeChoice`实现是否真的工作，这就完成了对您的组件的所有逻辑位的覆盖。

## 测试应变能力

那么，如果我们决定使用`id` s 作为键，其余的作为值，将`photos`属性从数组改为`Map`,会怎么样呢？代码的哪些部分必须改变才能适应？

```
const PHOTOS = new Map([
  [1, { url: '...', title: '...', description: '...' }]
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们知道演示者将是我们的第一个联系人，所以让我们确保将正确的数据传递给我们的组件:

```
export const photoListPresenter = ({ photos = new Map() }) => ({
  photos: Array.from(photos.entries()).map(([id, photo]) => ({ id, ...photo })),
  isListEmpty: photos.size > 0,
  Empty: EmptyList,
  List: PhotoList
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们还必须修正我们的测试来使用 pass `Map`而不是数组。幸运的是，我们只需要更改 presenter 测试，因为组件树的其余部分不会受到更改的影响。

```
describe(photoListPresenter, () => {

  it('should correctly determine an empty list', () => {
    const photos = new Map();

    expect(photoListPresenter({ photos }))
      .to.have.property('isEmptyList', true);

    expect(photoListPresenter({ photos }).photos)
      .to.eql([]); // is photos an array?
  });

  it('should correctly determine a populated list', () => {
    const photos = new Map([
      [1, { title: 'hello' }]
    ]);

    expect(photoListPresenter({ photos }))
      .to.have.property('isEmptyList', false);

    expect(photoListPresenter({ photos }).photos)
      .to.eql([{ id: 1, title: 'hello' }]); // is photos an array with id?
  });

}); 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在运行测试套件，所有测试都会通过，包括之前编写的模板测试。

如果`EmptyList`组件改变了，或者当您决定`PhotoList`应该将其列表呈现为一个`<canvas>`时，该怎么办？如果我们还需要显示一个微调器，而`photos`仍然作为 AJAX 调用的一部分被加载，该怎么办？如果照片也有需要在用户的本地格式化的相关的`Date`怎么办？

因为我们现在有了一个包含所有逻辑的函数，所以对于任何接近这段代码的人来说，在不影响组件的整个子树的情况下添加内容变得更加容易。不需要严格的政策或复杂的林挺规则，除了一点:将逻辑放入演示者，其余的放入模板。

## 结论

在生产中使用了这种模式后，我发现演示者提供了一种很好的方式来定义所有潜在的混乱部分的边界。它不需要渲染库来检查输出是什么，它只是处理数据。它不关心你是否使用 React，Preact，Inferno，Vue 或任何其他基于组件的库。presenter 测试在任何 JS 环境和测试框架中都运行良好，不需要伪造任何 DOM 或生成任何浏览器。

但是，这并不妨碍您为其余的表示组件构建浏览器测试。您仍然可以为所有其余的组件构建和运行测试，但是通过从它们中移除逻辑，您已经减少了您必须测试以确保全面覆盖的可能组合空间。

在我们自己的 Webpack、Babel、TypeScript 和 Preact-infined 项目中，我们在每一个`prepush` git 挂钩上运行这些测试，这意味着每次您试图推送到 GitHub 时都会运行这些测试。如果这些测试需要一分钟的时间来运行，你可以看到人们会如何想回避这个套件。

这样，由于测试在您按下 Enter 键后的 3 秒钟内运行，所以没有人会抱怨，并且在您修复测试之前，您不会再按下按钮，这有望归结为更改演示者。

安静点。

[![](../Images/46461ed01715217fec1816bcec2ad6a9.png)T2】](https://i.giphy.com/media/ef0zYcOodmbTMQZjkU/giphy.gif)