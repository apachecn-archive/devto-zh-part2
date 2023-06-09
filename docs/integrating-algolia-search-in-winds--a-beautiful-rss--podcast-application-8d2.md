# 在 Winds 中集成 Algolia 搜索——一个漂亮的 RSS 和播客应用程序

> 原文：<https://dev.to/nickparsons/integrating-algolia-search-in-winds--a-beautiful-rss--podcast-application-8d2>

[![Winds](img/f6e5a25f7e168fa12d526db2969dab4b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--izylzlIN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q6m6jqhqfstlsa6jo2hy.png)

今年早些时候， [Stream](https://getstream.io) 推出了 [Winds 2.0](https://getstream.io/winds) ，这是我们广受欢迎的面向 macOS、Windows 和 Linux 的开源原生应用程序的第二次迭代，它提供了一种全新的消费 RSS 订阅和播客的方式。如果你没有用过 Winds，你可以在 https://getstream.io/winds 的[报名。或者，如果你只是想要一个视觉效果，下面是 Winds 的截图:](https://getstream.io/winds)

[![Winds](img/435c43176f470b8b21ceb8d8121f7e96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--54CUn2PM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tqpyvnudwbuwdx7jdd8e.png)

对于我们正在编译和查询的所有内容，我们需要一种直观的方式来呈现内容供用户搜索。这就是 Algolia 出现的原因。Algolia 是一个易于使用的插件，允许像我们这样的开发人员为我们的用户创建独特的搜索和发现体验。最棒的是，Algolia 的速度非常快，可以从后端仪表板或前端代码进行配置，返回给用户的结果也非常相关。

在本帖中，我们将做一个快速的技术深度探究，看看我们是如何处理风的安装和配置的。如果你想跟进，Winds 的代码是一个开源项目，可以在 GitHub 上找到。

## 入门😇

Algolia 通过了解传统开发人员希望在对付费计划做出长期承诺之前试用产品，完善了他们的开发人员入职流程。考虑到这一点，他们通过提供开源选项的免费层来支持社区；唯一的要求是你在搜索栏中显示 Algolia 标志(如下图所示)。

[![Winds](img/c3c6180a5a5506524c0b538930455374.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_c8BHwLQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qnppzjbaemr661u0cwl9.png)

如果你对使用 Algolia 感兴趣，开始使用他们的免费开源计划，该计划提供 10，000 条记录和 100，000 次操作。只需填写此处的表格，Algolia 团队将会回复您——他们会很快回复您！

> 注意:这个职位不需要帐户。我们只是讨论风的集成，而不是讨论项目中的一般安装。然而，这篇文章可以作为在你自己的应用程序中安装 Algolia 的指南。

## 阿尔戈利亚一体化👨‍💻

与 Algolia 集成非常简单。因为我们用的是 Node.js，所以安装了 JavaScript 客户端([https://www . npmjs . com/package/Algolia search)(由 Algolia 提供](https://www.npmjs.com/package/algoliasearch)(provided))(Algolia search)。为了让事情变得更简单，我们创建了一个助手文件，可以很容易地将搜索结果(作为对象)传递给:

```
import algolia from 'algoliasearch';
import config from '../../config';
import util from 'util';
import logger from '../../utils/logger';

if (config.algolia.appId && config.algolia.writeKey && config.algolia.index) {
    const client = algolia(config.algolia.appId, config.algolia.writeKey);
    const index = client.initIndex(config.algolia.index);

    module.exports = async data => {
        if (!data.type) {
            throw new Error('Missing data.type key and value.');
        }
        await util.promisify(index.addObject.bind(index))(data);
    };

    module.exports.indexMany = async data => {
        await util.promisify(index.addObjects.bind(index))(data);
    };
} else {
    module.exports = async () => {
        logger.info('Faking search indexing');
    };

    module.exports.indexMany = function() {
        logger.info('Faking search indexing');
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

if 语句的第一部分用于生产或开发环境，因为它检查有效的 Algolia 密钥和秘密。后半部分用于伪造对 Algolia 的测试请求。

### 当我们需要添加单个对象时，在我们的一个控制器内部调用以下内容:

`await util.promisify(index.addObject.bind(index))(data);`

### 要添加多个对象，Algolia 支持使用以下代码进行批量插入:

`await util.promisify(index.addObjects.bind(index))(data);`

添加完所有内容后，它们会显示在我们的索引中(见下文)！

[![Winds](img/b8d8502ff37cd7e70aec8f7f63229598.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fi8dovrN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dx7dno62ld70fmsz6k7n.png)

## 从 Algolia 获取搜索结果🔎

在应用程序方面，有很多种方法可以搜索和浏览 Algolia 的数据。一个例子可能是 JavaScript 客户端 algoliasearch 您还可以选择使用更高级的、基于组件的抽象，比如 InstantSearch.js。需要注意的一点是，无论您使用何种方法，您都应该尝试从客户端获取结果，以减少延迟。

在我们的 React 应用程序中，我们获取结果并用下面的代码填充一个下拉菜单:

```
import Algolia from 'algoliasearch';

const client = Algolia(`APP_ID`, `SEARCH_API_SECRET`); // your api credentials
const index = client.initIndex(INDEX_NAME); // your index name (e.g. dev_winds)

index.search({ query: text, }, (err, results) => {
    if (err) {
        console.log(err);
        return;
    }

    this.setState({
        results: results.hits,
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

[![Winds](img/e29b5f7b77aae854727fd1cfd72f7e01.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t57LcCbZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cv2cpk3uzj025y3f1q4h.png)

与 Algolia 集成的美妙之处在于，您可以构建一个界面，以多种方式搜索和浏览您的数据。因为显示搜索结果所需的所有数据都已经存在于我们的 Algolia 记录中，所以呈现时的复杂性很小，所以我们可以避免可能会显著降低其他工具的速度或效率的方法。

## 同阿尔戈利亚的表现🏃‍

如前所述，我们从客户端获取结果。这是为了避免在我们通过 API 传输结果时可能遇到的任何延迟。不管查询是什么，Algolia 的搜索响应应该在低毫秒范围内。这要归功于 Algolia 背后的基础设施。这是一篇相当引人入胜的文章，如果你像我一样，你会想要阅读这篇文章，这篇文章涵盖了对阿尔戈利亚和 T2 的速度测试。

下面是一个截图，展示了 Algolia 的搜索功能在包含 70，000 多条记录的数据集上有多快(以毫秒计):

[![Winds](img/074fc2f59c9116aed7c51d61c6048d92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vbae1yta--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vxcex1990bb6magn7sq5.png)

## 最后的想法🤔

不管你的搜索需求如何，我们在 [Stream](https://getstream.io) 强烈推荐使用 Algolia。它快速、易于配置，由像您这样充满热情的开发人员构建。最后，我可以自信地说，我们玩得很开心，讨论了所有让我们在搜索中提供终极用户体验的可能性。

想了解更多关于风的信息，请关注未来关于科技和风的博客文章，并在 Twitter 上关注我，地址是 [@nickparsons](https://dev.to/nickparsons) 。如果你认为我错过了什么，请在下面的评论中留言。寻找快乐！🎉