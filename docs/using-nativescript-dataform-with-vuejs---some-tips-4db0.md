# 使用 Vue.js 的 NativeScript 数据表单-一些提示

> 原文：<https://dev.to/raymondcamden/using-nativescript-dataform-with-vuejs---some-tips-4db0>

正如我最近提到的，我目前正在为一个客户开发一个 [NativeScript Vue](https://nativescript-vue.org/) 应用程序，作为这项工作的一部分，我正在处理定制表单。我最初的方法利用了[定制组件](https://dev.to/raymondcamden/working-with-dynamic-components-in-vuejs-3bmh)，这让 Vue 变得相当琐碎，但我决定尝试一下 [NativeScript UI](http://docs.telerik.com/devtools/nativescript-ui/introduction) 。这是一组免费组件，包括以下功能:

*   日历
*   图表
*   列表视图
*   数据形式
*   侧抽屉
*   测量
*   自动完成

具体来说，[数据表单](http://docs.telerik.com/devtools/nativescript-ui/Controls/NativeScript/DataForm/dataform-overview)看起来可能有用。虽然用 NativeScript 构建表单并不太难，但 DataForm 试图尽可能地自动化这个过程。例如，考虑以下数据:

```
{
    "name":"Raymond Camden",
    "yearBorn":1973,
    "cool":true,
    "email":"raymondcamden@gmail.com"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在假设我们将它与一个数据表单控件联系起来:

```
<RadDataForm ref="dataForm" :source="person" /> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们真的就这样——控件会自动为我们呈现一个漂亮的表单:

[![Screenshot of form](img/a531680783448eea577103b17b298237.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PdxpeCam--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/df1.jpg)

请注意控件如何查看我的数据属性，并决定使用什么控件以及如何创建标签。例如`yearBorn`变成了`Year Born`。这一切都是默认发生的，非常酷，但是如果你不在乎它们的默认值，你也可以控制这一切。

总的来说，这是一个整洁的小控件，但当我开始尝试一些更高级的功能时，马上就遇到了一些问题。部分原因是由于一些糟糕的医生(我已经提交了报告！)我希望这篇文章可以帮助其他人避免我遇到的问题。

### 安装有 Vue 问题

所以文件告诉你安装相关的插件，但是之后事情就有点不对劲了。

Vue 文档和数据表单的[“入门”](http://docs.telerik.com/devtools/nativescript-ui/Controls/Vue/DataForm/getting-started)，它甚至没有被标注出来(在导航中它被称为“提供源代码”告诉你这样做:

将它添加到主 Javascript 或 Typescript 文件中，通常称为 main.js 或 main.ts:"

```
import RadDataForm from 'nativescript-ui-dataform/vue';

Vue.use(RadListView);
Vue.use(RadDataForm); 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我做到了。然后它说:

"在继续之前，请确保应用程序中需要 nativescript-ui-dataform/vue 模块。该模块处理 nativescript-vue 所需的自定义指令和元素的注册。

之后，只需将 RadDataForm 标记添加到 HTML 中，并相应地设置它的源:"

所以第一段没有意义。我是说，我不是已经做了吗？更糟糕的是，下面的代码示例没有提供任何额外的帮助。

我只能通过访问 NativeScript Playground，在页面上拖动一个数据表单控件，并观察它的作用来让事情正常运行。

基于此，这就是我的工作:

1.  不要给 main.js/main.ts.添加代码，在我看来这是不必要的。

2.  在你的组件中，需要这样的数据形式:

**编辑于 2018 年 11 月 7 日，发布后仅仅一个小时……[@ bundyo](https://dev.to/bundyo)将我原本的 5 行代码缩减为一行:**

```
import 'nativescript-ui-dataform/vue'; 
```

Enter fullscreen mode Exit fullscreen mode

看着这些代码，我上面引用的段落现在有意义了，但是我甚至不知道使用什么代码。如果页面上的代码样本包含了这一点，它将为我节省大约两个小时——我不骗你。

### 与组一起工作

好的——所以我看数据表单控件的主要原因是为了利用“组”的特性。这可以让你获得一个大的窗体，并创建可以打开和折叠的组。它本身不是一个“手风琴”控件，但它达到了同样的目的。(对于好奇的人来说，*是一个*的[本地脚本 Accordion](https://github.com/triniwiz/nativescript-accordion) 控件，但是它有一定的限制，使它不能用于我的情况。)下面是我从文档中窃取的两张截图——首先是 Android 版本:

[![Android example of Groups](img/0e729b98ece1ce153bbba3e6d77f519a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aTPx0Pfo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/df2.png)

然后 iOS:

[![iOS Groups example](img/c2ad6caed1ff3ba54a042983b5f2f877.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f3ZlPLwy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/df2a.png)

因此，虽然很酷，但这方面的文档非常少，特别是在提供动态组方面，我指的是在数据中定义的组，而不是硬编码为页面上的标签。我花了很多时间试图让它工作，最终放弃了，并向 NS Slack 组寻求帮助。谢天谢地 [@bundyo](https://github.com/bundyo) 来救援了。接下来是*他的*方案，不是我的。我的数据仍然是硬编码的，但是你可以看到哪里可以修改它，以支持从 Ajax 或类似的加载数据。

```
<template>
    <Page class="page">
        <ActionBar title="Demo" class="action-bar" />
        <ScrollView>
            <RadDataForm ref="dataForm" :source="album" :metadata="md" :groups="groups">
            </RadDataForm>
        </ScrollView>
    </Page>
</template>

<script>
import { RadDataForm, PropertyGroup } from 'nativescript-ui-dataform';

require("nativescript-vue").registerElement(
    "RadDataForm",
    () => require("nativescript-ui-dataform").RadDataForm
);

export default {
    data() {
        return {
            groups:[],
            album: {
                bandName: "Edwhat Sheeran",
                albumName: "X",
                year: "2017",
                owned: true,
                myRating: 9.5,
            },
            md:{

            }
        };
    },
    created() {

        this.md = {                 
            propertyAnnotations:[
                    {
                        name:"bandName",
                        displayName:"My band name",
                        required:true,
                        groupName:"Group One"
                    },
                    {
                        name:"albumName",
                        displayName:"My album",
                        required:true
                    },
                    {
                        name:"year",
                        required:true,
                        groupName:"Group One"
                    },
                    {
                        name:"owned",
                        required:true,
                        groupName:"Group Two"
                    },
                    {
                        name:"myRating",
                        required:true,
                        groupName:"Group Two"
                    }
                ]
        };

        let pg = new PropertyGroup(); 

        pg.name = "Group One"; 
        pg.collapsible = true;
        pg.collapsed = false;

        this.groups.push(pg);

        pg = new PropertyGroup(); 

        pg.name = "Group Two"; 
        pg.collapsible = true;
        pg.collapsed = true;

        this.groups.push(pg);

    }
};
</script>

<style scoped>
</style> 
```

Enter fullscreen mode Exit fullscreen mode

我们来分解一下。首先，看一下数据表单:

```
<RadDataForm ref="dataForm" :source="album" :metadata="md" :groups="groups">
</RadDataForm> 
```

Enter fullscreen mode Exit fullscreen mode

这里有两个新属性——`metadata`和`groups`。所以`metadata`是你可以覆盖控件的默认行为的地方。不喜欢它为你的房产价值选择的标签？你可以在这里调整它。想要使用带有特定值的自定义下拉列表吗？你可以在这里设定。我们使用这个特性来指定每个字段的组。(同样，这里是硬编码的，但它可以是动态的。)

下一步是创建组。在这种情况下，我们使用一个`PropertyGroup`实例，每个组一个，并确保名称与元数据中使用的名称相匹配。

如果你想看并玩一个更瘦的版本，可以看看这里制作的游乐场[@ bund yo](https://dev.to/bundyo):【https://play.nativescript.org/?template=play-vue】T2&id = qwbsl 5&v = 3 它真的很好地完成了设置组和场地的工作。因为它在操场上，你可以将 NativeScript Playground 应用程序指向它，并在 10 秒钟内让它在你的设备上运行。

[![Groups example running on phone](img/4cb40a09a850e84b38f3f90af5d3d971.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5qONGAYT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/11/df3.jpg)

无论如何，我希望这有所帮助。正如我所说的，这里的文档有点痛苦，但我已经向 NativeScript 人员发送了多个报告，因此希望它很快得到改进。如果你有任何问题，请在下面给我留言。