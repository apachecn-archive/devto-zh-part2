# FLOCSS CSS_design

> 原文：<https://dev.to/rinstarskykujat/-flocss-cssdesign-427m>

你好！我当工程师已经一年了。

自从我用 FLOCSS 制作 LP 以来，我描述一下我自己的制作方法和它的印象。
( [有样学地](http://rin-develop.jeez.jp/flocss/))

关于 FLOCSS
详情请见下面的知识库。https://github.com/hiloki/flocss/blob/master/README_eng.md
T2

主
[https://twitter.com/hiloki](https://twitter.com/hiloki)

更详细的用法等。请参考这篇文章。(仅 JP _ only)
[https://qiita.com/sueshin/items/dcbaf3d8a0adb6b087db](https://qiita.com/sueshin/items/dcbaf3d8a0adb6b087db)

我当前的开发环境

```
base
    _reset.scss
    _setting.scss
component
    I will add a component system scss file here.
fonts
    Font type file
helper
    mixin etc.
inc
    Import the part for each directory.
    _component.scss
    _layout.scss
    _page.scss
layout
    Layout system scss file is added here.
page
    Scss file in page units
style.scss
    Import whole file
vendor
    Dedicated directory such as external API 
```

为流程图创建的新布局

```
base
    No Change
fonts
    No Change
helper
    No Change
inc
    No Change
object
    layout　 l-Prefix scss file
    component　c-Prefix scss file
    project p-Prefix scss file
    utility u-Prefix scss file
page
style.scss
vendor 
```

# 简介

HTML 和 CSS 很容易崩溃，这经常是个问题。虽然它不能完全防止崩溃，但尝试通过引入设计规则来防止一点点崩溃，这就是这次引入的 CSS 设计规则，如 BEM、SMACSS 和 FLOCSS。
我自己有短暂的工程经验，但我已经意识到设计规则的重要性。

我想如果你在增加基本 CSS 部件的时候能看到“当前我的开发环境”
你就能理解了，把它添加到【component】目录下。
没有前缀等。，首席工程师会自由描述。
在目前的工作场所，任何时候最多有两个工程师，所以这种设计方法没有特别的问题，所以与其决定太详细的设计规则，任何人都可以很容易地参与到我正在做的建设中。

那么为什么，当我说我引入了 FLOCSS 的时候，如果你继续增加上面组件中的文件数量，那么这个文件在组件目录中大概就是 70 到 100 个文件。
如果是这个号，现在还不难追档。但是，如果将来您成为一个更大的站点，那么您会担心文件会无序地继续增加，
此外，当组件之间发生覆盖时，需要覆盖组件文件中的组件文件，这使得跟踪类名变得困难。

当你查看 advice readme 时，FLOCSS 在拥有基本规则的同时，在一定程度上认可了开发站点的可用性。
在我的例子中总是
布局
项目
组件
规则顺序就是描述的顺序。

```
Basic pattern

<div class="l-layout">
    <div class="p-project">
        <span class="c-component"></span>
    </div>
</div>

c-component When overwriting

<div class="l-layout">
    <div class="p-project">
        <span class="p-project_component c-component"></span>
    </div>
</div>

When attaching a modifier to c-component

<div class="l-layout">
    <div class="p-project">
        <span class="c-component -is-red"></span>
    </div>
</div> 
```

# 功过

功绩
布局
项目
组件
由于保护了组件的层次结构，所以组件等组件并不复杂。
通过积极地使其成为项目的一部分，最终的契合变得更加容易。
轻松找到文件
用 light 捆绑它让写 CSS 更容易。

缺点
多人开发时，对后来参与开发的人有必要做一些解释
因为有规则，没有共识就崩了。
最终，组件和项目的分离变成了个人的自由裁量权。
(我感觉任何设计理念都无法回避。)

# 采样地点

到目前为止，这只是一个简单的自我开发环境的解释，但是当它第一次被引入时，
这变成了项目？组件？，这让我很害怕。
现在的介绍结果有好几个，虽然在我自己身上可以在一定程度上做成规则，但我认为一开始就有很多混乱。
所以我将发布我假设 LP 的测试站点到这个 URL。

小心！！！因为它是从实际发布的站点转移过来的，原本存在的元素被移除，不必要的属性被应用。
我觉得如果可以参考类名的粒度和项目、组件的粒度而不是设计本身。
我认为它会被加入，即使只是一点点的尝试。

[http://rin-develop.jeez.jp/flocss/](http://rin-develop.jeez.jp/flocss/)

FLOCSS base _ static github
[https://github.com/TakuyaTaniguchi/static_develop](https://github.com/TakuyaTaniguchi/static_develop)