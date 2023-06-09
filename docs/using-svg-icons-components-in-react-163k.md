# 在 React 中使用 SVG 图标组件

> 原文：<https://dev.to/nishanbajracharya/using-svg-icons-components-in-react-163k>

SVG 很酷。他们扩大规模。它们按比例缩小。有时候他们会在你睡觉的时候杀了你，但是你知道，必要的邪恶之类的。

[![](img/0eba2425903518299ee8725f6cfdf104.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zDRyHINh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A7I6hcXUXbAxL5Uzn.) 

<figcaption>照片由[诺德伍德主题](https://unsplash.com/@nordwood?utm_source=medium&utm_medium=referral)上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

### **那么 SVG 有什么好的呢？**

SVG 或可缩放矢量图形是基于 XML 的图像格式，可以缩放到任何大小，同时保持图像的质量。因此，当你需要一个图像，需要缩放到你想要的大小时，SVG 是一个不错的选择。它们基本上是 XML 文档，因此与其他图像格式相比，它们的文件大小也很小。

它们也是有效的 XML 元素，可以使用 CSS 进行操作。所以在 SVG 上改变颜色和笔画都可以通过 CSS 来完成。

### **听起来不错。不好的东西呢？**

说到图像，SVG 非常适合简单的形状，充满了基本的笔画和颜色。任何比图标更复杂的东西都不值得争论。(除非你在做数据可视化，在这种情况下，让我把你指向 [D3.js](https://d3js.org/) 。)

自己构建 SVG 也更复杂。因为它们是用 XML 构造的，所以创建一个比存储像素数据的等效光栅图像更难。

[![](img/583c39d7612b8b190a6f29f648dd77bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LY0v9HmD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/915/1%2ACzphHhXBsKbTdOekRdngvg.png) 

<figcaption>缩放——光栅 vs 矢量</figcaption>

### **这一切的反应来自哪里？**

在 web 文档中使用 SVG 时，有两种选择。要么按原样呈现 SVG 文档，要么在 ***img*** 标签中将其用作源文件。更好的选择是按原样使用它，因为 image 标签中的 SVG 被呈现为图像，并且不能在 image 标签的 css 样式之外进行操作。

因此，当决定在 React 项目中使用 SVG 时，最好是构建一个组件，而不是将 SVG 呈现到文档中。

```
const Svg = () => 
 
   <path d="some path here" fill="#000" />
 ; 
```

这将在 html 文档中呈现一个静态 SVG。来加点道具吧。

```
const Svg = ({
 style = {},
 fill = '#fff',
 width = '100%',
 className = '',
 height = '100%',
 viewBox = '0 0 32 32',
}) => 
 
   <path d="some path here" fill={fill} />
 ; 
```

我们现在可以使用这个组件来呈现不同颜色、类名和样式的 SVG。查看下面的 CodeSandbox 演示。

[*链接到 CodeSandbox*](https://codesandbox.io/s/q79o54pxmj)

好了，现在我们对如何为 SVG 图标创建 React 组件有了一个大致的概念。我们如何处理大量的图标，这在大型项目中是很常见的？这里我们有多种选择。我们可以有一个返回所需 SVG 图标的巨型组件，或者创建一个 mapper 组件，它接受一个 prop 并将其映射到等价的 SVG 组件。

让我们来看看它们是如何实现的。

#### 方法#1

[*链接到 CodeSandbox*](https://codesandbox.io/s/ryy418r5km)

**TL；DR:** 我们创建一个单独的 SVG 组件，并传递一个名为 的 ***道具给它。该组件解析与图标相关联的视图框和路径值，并返回 SVG 元素。***

让我们从将名称 prop 添加到我们的 SVG 组件开始，并解析名称 prop 的路径。

```
const getPath = (name, props) => {
 switch(name) {
 case 'icon-1':
 return <path {...props} d="icon-1-path" />;
 case 'icon-2':
 return <path {...props} d="icon-2-path" />;
 default:
 return <path />;
 }
}

const Svg = ({
 name = '',
 style = {},
 fill = '#000',
 width = '100%',
 className = '',
 height = '100%',
 viewBox = '0 0 32 32',
}) => 
 
   {getPath(name, { fill })}
 ; 
```

很好。但是我们没有考虑到每个 SVG 图标可以有自己的视图框值。因此，我们还需要根据名称属性来解析视图框。

```
const getViewBox = name => {
 switch(name) {
 case 'icon-1':
 return 'icon-1-view-box'; // Eg. 0 0 32 32
 default:
 return '';
 }
}


 {getPath(name, { fill })}
; 
```

仅此而已。我们可以向这个组件添加更多的路径和视图框，并通过为我们需要的图标添加名称 prop 来使用它。

```
 
```

#### 方法二

[*链接到 CodeSandbox*](https://codesandbox.io/s/vvzkzwvp10)

**TL；DR:** 我们为每个 SVG 图标创建单独的文件，并创建一个基于 ***名称*** 属性返回 SVG 组件的索引文件。

我们为每个想要的 SVG 图标创建单独的组件。

```
./icons
--/Phone.js
--/Trash.js
--/Messages.js
--/Envelope.js
--/Wifi.js 
```

每个组件都是相互独立的，可以单独使用。

```
import Phone from './icons/Phone';

<Phone width={100} /> 
```

然后，我们创建一个索引文件，根据名称属性返回组件本身。

```
./icons
--/Phone.js
--/Trash.js
--/Messages.js
--/Envelope.js
--/Wifi.js
--/...
--/index.js 
```

索引文件看起来像这样。

```
import React from 'react';

import Phone from './Phone';
import Messages from './Messages';

const Icon = props => {
 switch(props.name) {
 case "phone":
 return <Phone {...props} />;
 case "messages":
 return <Messages {...props} />;
 default:
 return <div />;
 }
}

export default Icon; 
```

因此，每当我们需要添加新图标时，我们就创建新的组件，并将它们包含在索引文件中。我们通过导入单个图标组件并向其中发送名称为*的道具来使用该组件。*

```
import Icon from './icons';

<Icon fill="#49c" width={100} name="phone" /> 
```

仅此而已。我已经详细介绍了几种创建 React 组件来操作 SVG 图像的方法。当然，这些并不是 React 应用程序中处理 SVG 的唯一方式，甚至不是最好的方式。就像 Javascript 世界中的任何事情一样，我们总是有其他的选择。

#### 备选选项

[Webpack SVG 加载器](https://github.com/webpack-contrib/svg-inline-loader) —一个 [webpack](https://webpack.js.org/) 加载器，用于将 SVG 文件作为组件导入。

[React Inline SVG](https://github.com/gilbarbara/react-inlinesvg) —一个 React 组件，它将 SVG 文件路径作为在文档上呈现它们的道具。

这篇文章最后发表在媒体上。[在 React](https://blog.lftechnology.com/using-svg-icons-components-in-react-44fbe8e5f91) 中使用 SVG 图标组件

* * **