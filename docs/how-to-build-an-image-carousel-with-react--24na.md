# 如何用 React 构建一个图片轮播

> 原文：<https://dev.to/willamesoares/how-to-build-an-image-carousel-with-react--24na>

[![header-image](img/1fe79c363b1c56726c39637871f93653.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nY4t5Jgy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ce0rln8gpe1rppwj7udi.png)

嘿大家好！

在这篇文章中，我决定做一些我目前热衷的事情，React。

最近，我有机会在 React 上做了很多工作，随着 React 的优势对我来说越来越清晰，我越来越愿意投入到学习构建基于该库的用户界面的道路中。

您可能知道， [React](https://reactjs.org) 是一个 JavaScript 库，使用它您可以按照基于组件的方法构建复杂的交互式 ui。它使用的技术和概念专注于使与界面的交互成为更有效的任务。通过使用称为虚拟 DOM 的概念，它是真实 DOM 的轻量级表示，React 使与界面的交互成为一项非常快速的任务，因为对于每次更改，它都会将虚拟 DOM 与真实 Dom 进行比较，并只更新已更改的部分。

好吧，这只是 React 比其他库更强大的原因之一。在这篇文章中，我将关注一个使用 React 的真实例子，如果你发现自己在阅读代码时迷失了方向，或者你不知道这里提到的任何概念，你可以看看[文档](https://reactjs.org/docs/hello-world.html)。

#### 组件式思维

当你开始创建一个 UI 时，首先要做的事情之一就是把它想象成一组组件，这些组件将包装你的界面的一部分，然后一起工作来创建一个良好的用户体验，这是 React 对你做的事情之一，它改变了你组织你的应用程序的方式。

如果你看看这篇文章的标题图片，你会明白我们要在这里做什么。看起来真的很简单，对吧？事实上，这很简单，因为我们使用了 React:)

您应该知道，在 React 中有很多方法可以实现图像轮播，包括组织组件的方式，甚至可以创建多少个组件。对于这个例子，我决定创建三个基本组件，第一个是`Carousel`组件，它将是整个布局和逻辑的包装器，第二个是`ImageSlide`组件，它将简单地呈现轮播的图像幻灯片，第三个是`Arrow`组件，它将充当轮播过渡的左右箭头。因此，对于作为传送带输入的每张图片，您将拥有一个`ImageSlide`实例和两个`Arrow`组件实例，每个箭头一个。

首先，让我们构建`Carousel`组件。

#### `Carousel`分量

在做任何事情之前，您必须告诉 React 您希望它在 HTML 文件的哪个部分呈现您的组件。

在您的 HTML 文件中，添加以下内容:

```
<div id="container">
  <!-- Your component will be rendered here. -->
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在您的 JavaScript 文件中，输入这个:

```
ReactDOM.render(
  <Carousel />,
  document.getElementById('container')
); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们将 div 容器绑定到 Carousel 组件，因此 React 将使用该占位符来呈现整个组件。

请注意，在您的脚本中必须有可用的`React`和`ReactDOM`库。如果你在某种代码游戏中尝试这个，比如 [CodePen](https://codepen.io) 或者 [JsFiddle](https://jsfiddle.net) ，你可以简单地在设置部分添加相关的脚本。但是，如果您在本地机器上安装了 React 开发，您可能知道该怎么做:)

现在是时候设置我们的`Carousel`组件了。

```
class Carousel extends React.Component {
  render () {
    return (
      <div className="carousel"></div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们创建了一个简单的包装类，它将负责处理接口中的所有逻辑。这个类的 render 方法将负责返回整个布局的标记，所以我们将所有其他组件添加到返回的`div`块中。

您应该注意到，这里我们扩展了`React.Component`类，以声明这个特定的类将成为 React 组件。需要指出的是，这是与`React.createClass`方法等效的 ES6 方法。前者只是 ES6 特性集中提供的“语法糖”。这样，在实现组件方法时，您将有几个不同之处。查看托德莫斯特写得很好的[帖子](https://toddmotto.com/react-create-class-versus-component/#reactcreateclass)，了解每个选项的细节。

因为我们已经有了`Carousel`类的设置，我们应该开始看看我们要在里面渲染什么。

#### `ImageSlide`分量

这将是一个非常简单的组件，没有附加逻辑。这是在 React 中构建组件时的常见模式，这些类型被称为`stateless`或`functional`组件。其原因是由于不存在控制这些组件状态的逻辑，因为它们没有声明的状态。它们最终只是接收(或不接收)参数并返回(或不返回)基于这些输入值构建的标记的 JavaScript 函数。

让我们看看`ImageSlide`组件是什么样子的。

```
const ImageSlide = ({ url }) => {
  const styles = {
    backgroundImage: `url(${url})`,
    backgroundSize: 'cover',
    backgroundPosition: 'center'
  };

  return (
    <div className="image-slide" style={styles}></div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

可以看到这个组件没有太大的关系。它应该基本上接收图像的网址，并创建所需的标记，所以它将作为我们的轮播幻灯片之一。

`ImageSlide`是一个函数，它接收一个字符串，也就是图像的 URL，创建一个对象来描述组件的样式，并返回用我们提供的信息填充的标记。如上所述，对于您在图像数组中提供的每个 URL，您将拥有该组件的一个实例。

关于这个组件的样式，你可以看到图像被设置为它的背景。然而，你可以根据自己的意愿来设计你的组件，这不会影响这篇文章的目的。

因此，在为图像幻灯片创建标记之后，我们应该将它添加到`Carousel`组件的 render 方法中。

```
class Carousel extends React.Component {
  render () {
    return (
      <div className="carousel">
        <ImageSlide url={ imgUrl } />
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

URL 作为`props`对象中的属性传递给`ImageSlide`组件。在这篇文章的后面，我们将看到如何正确地从作为参考的图像数组中获取图像 URL。

#### `Arrow`分量

```
const Arrow = ({ direction, clickFunction, glyph }) => (
  <div
    className={ `slide-arrow ${direction}` }
    onClick={ clickFunction }>
    { glyph }
  </div> ); 
```

Enter fullscreen mode Exit fullscreen mode

这个组件甚至更简单，因为我们在返回它的标记之前不需要设置任何东西。我决定在这里使用三个属性，因为这个组件同时用于左箭头和右箭头。因此，它的实现应该更通用一些。属性将告诉组件每个实例使用哪个类(`left`或`right`)。`clickFunction`描述了当每个箭头被点击时会发生什么，最后`glyph`组件指的是这个组件的内容，也就是将要呈现的内容。

这样，让我们将两个箭头都添加到`Carousel`组件中。

```
class Carousel extends React.Component {
  render () {
    return (
      <div className="carousel">
        <Arrow
          direction="left"
          clickFunction={ this.previousSlide }
          glyph="&#9664;" />

        <ImageSlide url={ imgUrl } /> 
        <Arrow
          direction="right"
          clickFunction={ this.nextSlide }
          glyph="&#9654;" />
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

从这里我们可以更好地了解我们的 carousel 的最终标记。

接下来，我们应该回到`Carousel`组件，因为还有几件事情要完成。

您会注意到我们正在向`Arrow` s 组件传递两个不同的函数。那些负责处理幻灯片转换的人。但是他们是怎么做到的呢？

首先，我们应该查看我们需要创建的状态，以便告诉包装组件每次应该呈现哪个图像。因此，让我们设置`constructor`函数，这样我们就可以为`Carousel`组件创建初始状态。

```
class Carousel extends React.Component {
  constructor (props) {
    super(props);

    this.state = {
      currentImageIndex: 0
    };
  }

  render () {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

在`constructor`函数中要做的第一件事是调用`super()`,将道具作为参数传递给它，以防您想在这个上下文中通过`this`关键字访问属性。现在，发送`props`对象是可选的，因为我们没有在构造函数中使用 props。

最初，我们将名为`currentImageIndex`的状态设置为`0`。这将保存每次必须在屏幕上呈现的图像的当前索引。这里我们从图像数组中的第一幅图像开始。

这个状态将用于获取传递给`ImageSlide`组件的正确 URL。让我们来看看如何做到这一点。

```
class Carousel extends React.Component {
  constructor (props) {...}

  render () {
    return (
      <div className="carousel">
        <Arrow .../>

        <ImageSlide url={ imgUrls[this.state.currentImageIndex] } /> 
        <Arrow .../>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这之后，我们所要做的就是告诉组件如何根据用户交互来更新状态。这项工作与我们的`Arrow`组件相关，由于我们已经将两个函数(`previousSlide`和`nextSlide`)作为属性传递，现在我们必须实现它们。

你会注意到这两个函数是相似的。它们所做的就是通过添加或删除来将`currentImageIndex`状态更新 1。这里只指出一个细节。有些情况下，我们将不得不重置当前索引的值，因为它将在某个点达到图像数组的最大或最小索引。因此，为了知道我们是否应该重置索引，检查数组的长度是很重要的。

```
class Carousel extends React.Component {
  constructor (props) {...}

  previousSlide () {
    const lastIndex = imgUrls.length - 1;
    const { currentImageIndex } = this.state;
    const shouldResetIndex = currentImageIndex === 0;
    const index =  shouldResetIndex ? lastIndex : currentImageIndex - 1;

    this.setState({
      currentImageIndex: index
    });
  }

  nextSlide () {
    const lastIndex = imgUrls.length - 1;
    const { currentImageIndex } = this.state;
    const shouldResetIndex = currentImageIndex === lastIndex;
    const index =  shouldResetIndex ? 0 : currentImageIndex + 1;

    this.setState({
      currentImageIndex: index
    });
  }

  render () {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

对于`previousSlide`函数，您可以注意到复位条件被设置为`currentImageIndex`状态为`0`的情况，这意味着如果索引指向数组中的第一个图像，然后用户单击左箭头，那么索引应该指向数组中的最后一个图像(`imgUrls.length - 1`)。

`nextSlide`做的完全一样，不同的是，如果索引当前指向最后一幅图像，那么它应该被重置为指向第一幅(`index = 0`)。

在所有剩余的情况下，这两种方法都简单地将所提到的状态改变一个，以便获得前一个或下一个图像。

最后，这里有一件重要的事情需要注意。为了更新 React 组件中的状态，我们必须使用`setState`方法。该方法负责告诉 React 应该更新该组件及其子组件。这是更新用户界面的主要方式。

因此，每当你点击左或右箭头时，你基本上是在更新`currentImageIndex`的状态，从而用新的图像幻灯片更新你的界面。

然而，为了在这两个函数中访问`this`关键字，您必须正确地将上下文绑定到它们。一种常见的方法是在组件的`constructor`方法中。

```
class Carousel extends React.Component {
  constructor (props) {
    ...

    this.nextSlide = this.nextSlide.bind(this);
    this.previousSlide = this.previousSlide.bind(this);
  }

  previousSlide () {...}

  nextSlide () {...}

  render () {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们已经实现了 carousel 组件的所有逻辑。你可以看看[这个 codepen](https://codepen.io/willamesoares/full/PQpEzx/) 里面的工作演示和完整版代码。

PS1。:由于这不是这篇文章的目的，我省略了组件的样式，因此如果你对此感兴趣，你应该看看上面提到的 codepen。

PS2。:我仍然想在幻灯片过渡中添加一些动画，但我想以 React 的方式正确地完成它，所以这可能会是一个新帖子:)

感谢阅读，伙计们！