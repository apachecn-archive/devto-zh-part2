# 了解 QuillJS -第 1 部分(羊皮纸、印迹和生命周期)

> 原文：<https://dev.to/charrondev/getting-to-know-quilljs---part-1-parchment-blots-and-lifecycle--3e76>

这是 QuillJS 及其数据库羊皮纸系列博客的第一篇。下面的后续文章是计划中的，完成后会链接到这里。

1.  [羊皮纸、印迹和生命周期](https://dev.to/charrondev/getting-to-know-quilljs---part-1-parchment-blots-and-lifecycle--3e76)
2.  容器-创建多行块
3.  内嵌-创建一个@提及污点
4.  块嵌入-创建一个没有 iFrame 的自定义视频污点

注意:这个系列的目标读者是那些试图深入了解羽毛笔和羊皮纸的人。如果你只是想开始使用一个简单、功能完善的编辑器，看看奎尔的[快速入门指南](https://quilljs.com/docs/quickstart/)或[带羊皮纸指南的克隆媒介](https://quilljs.com/guides/cloning-medium-with-parchment/)可能是个好主意。

## 什么是鹅毛笔？

QuillJS 是一个现代的富文本编辑器，具有兼容性和可扩展性。它由[陈以桐](https://twitter.com/jhchen)和[拜伦·米利根](https://twitter.com/byronmilligan)创建，由 Salesforce 开源。从那时起，它已经被数百家其他公司和个人用来在浏览器中构建快速、可靠和丰富的编辑体验。

Quill 是一个主要包含电池的库，支持最常见的格式选项，如**粗体**、*斜体*、~~下划线、自定义字体和颜色、分隔符、标题、`inline code`、代码块、块引号、列表(项目符号、编号、复选框)、公式、图像以及嵌入的视频。~~

## 你还想要什么？

几个月前，我工作的公司 [Vanilla Forums](https://github.com/vanilla) 开始为我们的产品计划一个新的编辑器。我们当前的编辑器支持许多不同的文本输入格式，包括

*   降价
*   BBCode
*   超文本标记语言
*   所见即所得 HTML(使用 iFrame 呈现内容)

对于所有这些格式，我们有不同的解析器、呈现器和前端 javascript，所以我们开始创建新的编辑器，用一个新的统一、丰富的编辑体验来替换它们。

我们选择 Quill 作为新编辑器的基础，是因为它的浏览器兼容性和可扩展性，但我们很快意识到它不会提供我们需要的所有功能。值得注意的是缺少多行块类型结构，如块引号(缺少嵌套和多行支持)。我们有一些其他格式的项目，如扰流片有类似的要求。

我们还以丰富的链接嵌入形式添加了一些扩展功能，以及针对图像和视频的特殊格式选项和功能。

所以我着手去了解羽毛笔和它的底层数据库 T2 羊皮纸。这一系列的帖子代表了我对羊皮纸和 QuillJS 的理解。我不是项目的维护者，所以如果这里有什么不正确的地方，我鼓励你指出来。

## 数据格式

Quill 有两种数据格式。[羊皮纸](https://github.com/quilljs/parchment)(印迹)，和[三角洲](https://github.com/quilljs/delta)。

羊皮纸被用作内存中的数据结构，主要由树形结构的链表组成。它的印迹树应该与浏览器的 DOM 节点树 1:1 映射。

增量用于存储来自编辑器的持久数据，并采用相对扁平的 JSON 数组的形式。数组中的每一项都代表一个操作，它可以影响或代表多个 DOM 节点或印迹。这是通常存储在数据库或持久存储中的数据形式。它也用来表示一种状态和另一种状态之间的区别。

## 什么是污点？

印迹是羊皮纸文件的组成部分。它们是 Quill 最强大的抽象之一，因为它们允许编辑器和 API 用户消费和修改文档的内容，而不需要直接接触 DOM。印迹有一个比 DOM 节点更简单和更有表现力的界面，这使得消费和创建它们更容易推理。

每个印迹必须实现接口`Blot`，并且鹅毛笔和羊皮纸中的每个现有印迹都是从`ShadowBlot`继承的类。

为了能够从印迹的角度查看文档，每个印迹都有以下参考

*   `.parent` -包含此污点的污点。如果这个印迹是顶级印迹，`parent`将是`null`。
*   `.prev` -该印迹的父节点在树中的前一个兄弟印迹。如果这个 iBlotis 是它的第一个直接下级`parent`，`prev`将是`null`。
*   树中的下一个兄弟污点形成这个污点的父节点。如果这个污点是直接在其`parent`下的最后一个孩子，`next`将是`null`。
*   卷轴是羊皮纸数据结构中的顶级印迹。关于卷轴污点的更多信息将在后面提供。
*   `.domNode` -因为羊皮纸的树与 DOM 的树 1:1 映射，所以每个污点都可以访问它所代表的`Node`。此外，这些 DOM 节点将引用它们的印迹(带有`.__blot`)。

## 印迹生命周期

每个印迹都有几个“生命周期方法”，您可以在过程中的特定时间覆盖这些方法来运行代码。不过，在插入您自己的定制代码之前或之后，您通常仍然希望调用`super.<OVERRIDEN_METHOD>`。这个组件生命周期分为多个部分。

### 创作

正确创建一个污点有多个步骤，但是这些都可以用调用`Parchment.create()`来代替

#### `Blot.create()`

每个印迹都有一个从初始值创建 DOM 节点的`static create()`函数。这也是在与实际的 Blot 实例无关的 DOM 节点上设置初始值的好地方。

返回的 DOM 节点实际上并没有附加到任何地方，并且印迹仍然没有被创建。这是因为印迹是从一个 DOM 节点的创建的*，所以这个函数会把一个放在一起以防还没有。印迹不一定总是用它们的创建功能构建的。例如，当用户复制/粘贴文本(从 Quill 或从其他来源)时，复制的 HTML 结构被传递到`Parchment.create()`。羊皮纸将跳过调用 create()并使用传递的 DOM 节点，跳到下一步。* 

```
import Block from "quill/blots/block";

class ClickableSpan extends Inline {

    // ...

    static tagName = "span";
    static className = "ClickableSpan";

    static create(initialValue) {
        // Allow the parent create function to give us a DOM Node
        // The DOM Node will be based on the provided tagName and className.
        // E.G. the Node is currently <code class="ClickableSpan">{initialValue}</code>
        const node = super.create();

        // Set an attribute on the DOM Node.
        node.setAttribute("spellcheck", false);

        // Add an additional class
        node.classList.add("otherClass")

        // Returning <code class="ClickableSpan otherClass">{initialValue}</code>
        return node;
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

#### `constructor(domNode)`

获取一个 DOM 节点(通常在`static create()`函数中创建，但并不总是如此)并从中创建一个污点。

这是实例化任何你想在污点内部保存引用的东西的地方。这是一个注册事件监听器或做任何您通常在类构造函数中做的事情的好地方。

调用构造函数后，我们的污点仍然不在 DOM 树或羊皮纸文档中。

```
class ClickableSpan extends Inline {

    // ...

    constructor(domNode) {
        super(domNode);

        // Bind our click handler to the class.
        this.clickHandler = this.clickHandler.bind(this);
        domNode.addEventListener(this.clickHandler);
    }

    clickHandler(event) {
        console.log("ClickableSpan was clicked. Blot: ", this);
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 登记

羊皮纸记录了你所有的印迹，以简化它们的创建。使用这个注册表，羊皮纸公开了一个函数`Parchment.create()`，它可以从它的名字——使用 Blot 的`static create()`函数——或者从一个现有的 DOM 节点创建一个 Blot。

为了使用这个注册表，你需要使用`Parchment.register()`注册你的印迹。使用 Quill 最好使用`Quill.register()`，它将在内部调用`Parchment.register()`。关于鹅毛笔`register`功能的更多细节，参见[鹅毛笔优秀文档](https://quilljs.com/docs/api/#register)。

```
import Quill from "quill";

// Our Blot from earlier
class ClickableSpan extends Inline { /* ... */ }

Quill.register(ClickableSpan); 
```

Enter fullscreen mode Exit fullscreen mode

#### 确保印迹具有唯一的标识符

当用`Parchment.create(blotName)`创建一个 Blot 并传入一个对应于寄存器`blotName`的 sting 时，你将总是得到正确的实例化类。你可以有两个不同的印迹名称相同的印迹，`Parchment.create(blotName)`将正确工作。然而，当使用方法`Parchment.create(domNode)`的其他形式时，可能会出现未定义的行为。

虽然您可能知道手动实例化一个污点时的`blotName`，但是有些情况下 Quill 需要从 DOM 节点创建一个污点，比如复制/粘贴。在这些情况下，你的印迹需要用两种方法中的一种来区分。

#### 按标记名

```
import Inline from "quill/blots/inline";

// Matches to <strong ...>...</strong>
class Bold extends Inline {
    static tagName = "strong";
    static blotName = "bold";
}

// Matches to <em ...>...</em>
class Italic extends Inline {
    static tagName = "em";
    static blotName = "italic";
}

// Matches to <em ...>...</em>
class AltItalic extends Inline {
    static tagName = "em";
    static blotName = "alt-italic";

    // Returns <em class="alt-italic">...</em>
    static create() {
        const node = super.create();
        node.classList.add("Italic--alt");
    }
}

// ... Registration here 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，当传递带有标签`em`或`strong`的 DOM 节点时，羊皮纸可以很容易地区分`Bold`和`Italic`墨迹，但是无法区分`Italic`和`AltItalic`。

目前，羊皮纸区分这些 HTML 结构的唯一方法是设置一个`static className`来匹配传入的 DOM 节点上的预期 CSS 类。如果没有提供，您可能会发现自己通过其`blotName`手动创建了一个自定义污点的实例，却发现撤销/重做或复制/粘贴操作将您的污点更改为不同的类型。这在使用普通的`tagName`如`span`或`div`时尤为常见。

#### 按类名

```
// ... Bold and Italic Blot from the previous example.

// Matches to <em class="alt-italic">...</em>
class AltItalic extends Inline {
    static tagName = "em";
    static blotName = "alt-italic";
    static className = "Italic--alt";

    // Returns <em class="alt-italic">...</em>
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，已经设置了`static className`。这意味着父节点`ShadowBlot`将自动将`className`应用到`static create()`函数中元素的 DOM 节点，并且羊皮纸将能够区分两个印迹。

* * *

### 插入和附着

既然已经创建了一个污点，我们需要将它附加到 Quill 的文档树和 DOM 树中。有多种方法可以在文档中插入污点。

#### `insertInto(parentBlot, refBlot)`

```
const newBlot = Parchment.create("someBlotName", initialBlotValue);
const parentBlot = /* Get a reference to the desired parent Blot in some way */;
newBlot.insertInto(parentBlot); 
```

Enter fullscreen mode Exit fullscreen mode

这是主要的插入方法。其他的插入方法都调用这个。它处理将一个污点插入到父污点中。默认情况下，该方法会将`newBlot`插入到`parentBlot`的子元素的末尾。它的 DOM 节点也将被追加到`parentBlot.domNode`。

如果`refBlot`也被通过，那么`newBlot`将被插入到父节点中，除了不是被插入到`parentBlot`的末尾，而是被插入到`refBlot`之前，并且`newBlot.domNode`将被插入到`refBlot.domNode`之前。

此外，使用`attach()`方法将在该调用结束时设置`newBlot.scroll`。细节可以在这篇文章的后面找到。

#### `insertAt(index, name, value)`

此方法仅适用于从`ContainerBlot`继承的印迹。稍后的帖子将会更详细地介绍`ContainerBlot`，但是这些污点中最常见的是`BlockBlot`、`InlineBlot`和`ScrollBlot`。`EmbedBlot`和`TextBlot`不继承`ContainerBlot`。

这个方法会用传递的`name`和`value`为你调用`Parchment.create()`。新创建的印迹将被插入给定的`index`。如果在给定的索引处有嵌套的容器，调用将被传递到树中最深处的容器，并被插入到那里。

#### `insertBefore(childBlot, refBlot)`

这种方法和`insertInto()`类似，只是反过来了。不是子元素将自身插入父元素，而是父元素将子元素插入自身。在内部，`insertInto()`被调用，`refBlot`在这里服务于相同的目的。

#### `attach()`

`attach()`将调用印迹的父节点的`ScrollBlot`作为`.scroll`属性附加到自身。如果调用 Blot 是一个容器，在设置自己的`ScrollBlot`之后，它还会调用它的所有子节点上的 attach。

* * *

### 更新和优化

*注:我对羊皮纸这部分的理解还不完全。当我获得更好的理解时，我会在将来更新它。如果有人能帮忙填补空白，尤其是关于 optimize()可能调用子对象的次数，我们将不胜感激。*

`ScrollBlot`是顶级`ContainerBlot`。它保存所有其他的污点，并负责管理在 [contenteditable](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Editable_content) 内部所做的更改。为了控制编辑器的内容，`ScrollBlot`设置了一个[变异观测器](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver)。

`ScrollBlot`跟踪[的突变记录](https://developer.mozilla.org/en-US/docs/Web/API/MutationRecord)并在每个印迹上调用`update()`方法，该印迹的 DOM 节点是`MutationRecord`的`target`。相关的变异记录作为参数传递。此外，每个`update`调用都会传递一个共享上下文。

然后`ScrollBlot`获取相同的突变记录，并对每个受影响的污点*以及该污点的每个子节点递归调用`optimize()`方法，直到树的底部*。传递相关的变异记录以及相同的共享上下文。

#### `update(mutations: MutationRecord[], sharedContext: Object)`

使用针对其 DOM 节点的突变记录来调用 Blot 的更新方法。在单个更新周期中，在每个印迹之间共享单个上下文。

该方法在不同的核心印迹中有 3 种主要的实施方式。

##### 集装箱

`ContainerBlot`检查修改其直接子节点的变更，并将:

*   从已删除 DOM 节点的文档中移除墨迹。
*   为已经添加的 DOM 节点添加印迹。

如果添加了一个不匹配任何注册印迹的新 DOM 节点，容器将删除该 DOM 节点，并用与最初插入的 DOM 节点的文本内容对应的 DOM 节点(基本上是一个纯文本印迹)替换它。

##### 文字印迹

当它存在于 DOM 树中时，`TextBlot`将用 DOM 节点中的新内容替换它的`value`。

羊皮纸里的`EmbedBlot`没有实现`update()`。羊皮纸的`EmbedBlot`和它在 Quill `BlockEmbed`中的后代类都不能控制它们的子 DOM 节点的突变。

Quill 的另一个`EmbedBlot`后代类`Embed`用零宽度空格字符包装其内容，并在内部子类上设置`contenteditable=false`。在它的`update()`方法中，它检查一个突变记录是否会影响这些空格字符的`characterData`。如果是的话，印迹会恢复受影响节点的原始字符数据，并将更改作为文本插入到其本身之前或之后。

#### `optimize(context)`

在一次更新完成后调用`optimize()`方法。需要注意的是，`optimize`调用应该 ***永远不要*** 改变文档的长度或值。然而，这是降低文档复杂性的好地方。

为了简化，文档的`Delta`在优化过程之前或之后应该总是相同的。

默认情况下，印迹只清理更新过程中剩余的数据，尽管有一些印迹在这里做了一些额外的更改。

##### 容器

Empty `Containers`要么移除它们自己，要么添加回它们的默认子节点。因为文档的长度在更改前后必须相同，所以默认的子印迹必须是长度为 0 的子印迹。就奎尔的污点而言，那个孩子是个突破。

##### 行内和列表

Quill 的`Inline`和`List` Blots 都使用优化来简化 DOM 树并使其更加一致。

作为一个例子，相同的增量

```
[  {  "insert":  "bold",  "attributes":  {  "bold":  true  }  },  {  "insert":  "bold italic",  "attributes":  {  "bold":  true,  "italic":  true  }  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

可以用三种不同的方式呈现。

```
<strong>bold</strong><strong><em>bold italic</em></strong>
<!-- or -->
<strong>bold</strong><em><strong>bold italic</strong></em>
<!-- or -->
<strong>bold<em>bold italic</em></strong> 
```

Enter fullscreen mode Exit fullscreen mode

增量是相同的，这通常会以几乎相同的方式呈现，但是在 FormatBlot 中的[优化实现确保这些项目总是一致地呈现。](https://github.com/quilljs/quill/blob/develop/blots/inline.js#L31-L40)

* * *

### 删除和脱离

#### `remove()`

`remove()`方法通常是完全删除污点及其 DOM 节点的最简单方法。它从 DOM 树中移除污点的`.domNode`，然后调用`detach()`。

#### `removeChild(blot)`

这个方法只在`ContainerBlot`及其子类上可用。从调用印迹的`.children`中移除传递的印迹。

#### `deleteAt()`

删除指定索引处的污点或内容。在内部调用`remove()`。

#### `detach()`

删除所有引用奎尔已经吸干。这包括使用`removeChild()`从其父节点移除污点。如果适用，还对任何子印迹调用`detach()`。

* * *

## 包装完毕

这就结束了主要的生命周期。其他印迹方法，如`replace()`、`replaceWith()`、`wrap()`和`unwrap()`，将在本系列的下一篇文章“容器-创建多线模块”中介绍。

* * *

## 如果你喜欢这篇文章，请保持联系！

*   [加入我在 LinkedIn 上的职业关系网](https://www.linkedin.com/in/adam-charron-278678135/)
*   [在 Twitter 上关注我](https://twitter.com/CharronDev)
*   在 Dev.to 上跟着我