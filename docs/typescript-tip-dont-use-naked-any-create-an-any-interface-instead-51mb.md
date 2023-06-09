# Typescript 提示:不要使用“裸 any”，而是创建一个“any 接口”。

> 原文：<https://dev.to/tanepiper/typescript-tip-dont-use-naked-any-create-an-any-interface-instead-51mb>

Typescript 提供的 get out 子句之一是在您的代码库中使用 any 类型。在创建我们的第一个实现时，我们可能不知道我们对象的完整形状，我们可能会试图用大量的逻辑来过载我们的函数

[![](img/09afa827e14588310f53dec58a97d75c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F7zzUy1W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6sayzse4kvy4zisfq91y.png)

这段代码在某些方面不是很有描述性，至少就 Typescript 而言是这样。我们可以创建一个接口，让代码更具描述性，同时在开发过程中保持灵活性，而不是使用任何这种接口。

[![](img/ed2879022c33def64c05f994fe3412ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NB1FLW2g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ydu1mhbpn9eoxhtu2gvz.png)

这已经好多了！我们知道我们将得到一个旧的模型，我们将得到一个新的模型，所以在我们代码的后面，我们将能够调用我们可能附加到它的方法和属性。编译器也不会抱怨，并让你能够专注于增强你的类型。

另一个额外的好处是在代码中，您可以在任何地方按住 Alt 或 Ctrl 键单击界面，以查看它的形状，并查看您是否缺少属性。

但是随着我们的模型开始被使用，我们开始看到我们想要创建的类型。

[![](img/ba5b154d50073a1715707ca259a16503.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fWgS1FQz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ypdfgplu5w2kbhcmoip4.png)

我们还没有提取完旧模型的所有特征，所以我们在主对象上留下了[key:string]: any。属性也接受这一点，因为我们希望保持这种灵活性。如果需要，您可以更严格地将值设置为特定类型的列表，如:

[![](img/9b8b1eb4f8a197715716b8f21eae75df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k5NY_xC---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i6w3o2sl2q4hcwf4nazr.png)

这将至少确保您的财产被合理地严格执行到这些类型之一。

您甚至可以更进一步，为您的类型创建一个类:

[![](img/74f99a358548b83e1bbf0109acea69d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZbKThoVk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o8589a34x144bspg8twp.png)

我们的新模型，我们想锁定，所以我们删除它。从这里开始，您可以使您的代码更具可读性、可测试性，并且可以从复杂的逻辑向下重构到类型比较和对象创建。

希望使用这种技术也能帮助你的代码库！