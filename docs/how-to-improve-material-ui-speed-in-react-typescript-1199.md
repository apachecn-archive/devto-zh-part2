# 使用材质 ui 时，如何提高 React-Typescript 中的构建速度

> 原文：<https://dev.to/janpauldahlke/how-to-improve-material-ui-speed-in-react-typescript-1199>

你好，
你在 react typescript 中使用 material - ui 时遇到了性能问题？好吧，我在同一个问题上被难住了。自从我开始使用 material-ui 以来，构建就像永远都在进行，我真的不喜欢为这样一个蹩脚的项目编码。

但是让我们进入媒体研究，描述一下这个场景:

我写了一个这样的头组件
[头](https://i.imgur.com/3pMF0hO.png)。很明显，对吧？

在编码的时候，我真的很不开心，因为构建和 livereolad 变得相当慢。所以我摆弄了一下 Typescript 编译器，找到了这个小命令
`tsc --diagnostics --listFiles`，我在我的 package.json 中给它起了个别名`healtcheck`

运行它是痛苦的，结果是这样的

```
Files:          5598
Lines:        108833
Nodes:        453997
Identifiers:  146625
Symbols:      217251
Types:         56743
Memory used: 300772K
I/O read:      4.66s
I/O write:     0.48s
Parse time:   26.90s
Bind time:     1.62s
Check time:    9.72s
Emit time:     1.64s
Total time:   39.87s 
```

正如你们可以很容易地看到，这需要相当长的时间，并运行到一吨的文件。

我为标题导入了两个图标，如下所示:

```
import {
  AccountCircle,
  Language
} from '@material-ui/icons'; 
```

我想知道`--diagnostics`在构建时考虑的大量文件，尤其是数万亿的`/node_modules/@material-ui/icons/**.d.ts`。

是的，我是一个 typescript 用户，虽然我有一些麻烦，但我现在真的很喜欢它。正如你可能知道的，如果一个包没有自带类型，github 上有一个巨大的社区[definitely typed](https://github.com/DefinitelyTyped/)，为大的包提供类型。

例如:人们将得到如下的 material-ui 类型:`npm install --save-dev @types/material-ui`，结果是能够在 typescript 世界中使用 material-ui。

但是将近 40 秒的构建时间？我想改善这一点，并在老大哥做了一些研究。
发现这个 github [问题](https://github.com/mui-org/material-ui/issues/11916)指出了同样的问题。我真的很高兴看到这个有重大改进的[评论](https://github.com/mui-org/material-ui/issues/11916#issuecomment-429984950)。

我没有像上面那样导入，而是简单地将图标的导入重写为:

```
import AccountCircle from '@material-ui/icons/AccountCircle';
import  Language from '@material-ui/icons/Language'; 
```

现在让我们看看这对我们的`healtcheck`有多大影响:

```
Files:           382
Lines:         98396
Nodes:        386176
Identifiers:  130972
Symbols:      201601
Types:         56743
Memory used: 245687K
I/O read:      0.35s
I/O write:     0.37s
Parse time:    4.29s
Bind time:     0.79s
Check time:    5.16s
Emit time:     1.44s
Total time:   11.69s 
```

哇！我对这样一个小小的变化无言以对，但却有这样的影响。我们通过修改 2 行获得了将近 30 秒的时间，我的意思是可以有更多行，但是通过**不通过索引**导入。

我很高兴能和你分享这些，也许会对其他人有所帮助。

我的名字是简保罗，我为即时数据公司工作。我们是一家小公司，为大型机构提供内部工具和数据交换平台。

愿代码与你同在:祈祷:
感谢标题图片给[埃米尔·布鲁克纳](https://unsplash.com/@emilbruckner) @unsplash。