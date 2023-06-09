# 和橡皮鸭说话

> 原文：<https://dev.to/briannipper/talk-to-the-rubber-duck-2gmf>

[![](img/bc012a36fc3e08dc9ace5d41f3638d4a.png)](https://3.bp.blogspot.com/-CkfYFe6C0w8/W5prSLrQo1I/AAAAAAAAHKk/fO3bcD_9Q1wzhIidxCxVk7bj9K6SIxaawCLcBGAs/s1600/640px-Rubber_duckies_So_many_ducks.jpg)
| *见图片底部的归属。|

我有幸参与的最伟大的技巧之一是，仅仅通过倾听来帮助别人解决问题。我相信对这种现象有更准确的描述，但在编程的世界里它被称为[橡皮鸭调试](https://en.wikipedia.org/wiki/Rubber_duck_debugging)。我第一次接触到这个想法是在听[杰夫·阿特伍德](https://en.wikipedia.org/wiki/Jeff_Atwood)的播客/采访以及阅读他在[CodingHorror.com](https://blog.codinghorror.com/)下的博客时，他写的标题为[鲁伯鸭问题解决](https://blog.codinghorror.com/rubber-duck-problem-solving/)的文章。

为什么叫橡皮鸭？实际上，这与橡皮鸭没有任何关系，其目的是鼓励人们通过描述实际问题来解决他们自己的问题。当人们发现仅仅是试图描述一个问题的行为就能帮助他们找到他们正在寻找的答案时，他们通常会感到惊讶。

所以我帮助别人的方法是问一些我经常问自己的问题，来帮助他们完成橡皮鸭过程。例如，如果一个开发人员在某个特定的 bug 上向我寻求帮助，我问他们的问题看起来是显而易见的，但令人惊讶的是，他们的回答往往会带来答案。

那么你应该问什么类型的问题呢？最明显的是“问题是什么”这个问题通常可以帮助你找到问题的根源并解决问题。让我们通过一个虚构的例子来证明这一点。

一名开发人员的应用程序与第三方 API 服务通信时遇到问题。这就是“显而易见”的问题，但现在下一个问题来了，你准备好了吗...“为什么会出现这个问题？”

那么开发者怎么回答这个问题呢？调试是一个很好的起点。一个很好的起点是尝试在本地重现该问题。值得注意的是，在我们的例子中，这不会导致在服务器上发现的问题重现。现在在某些情况下，例如当开发者不被允许远程访问受影响的服务器时，仍然有很多被发现。为什么我们现在知道应用程序不是问题所在，API 服务也没有损坏。这指向一个环境问题。因此，即使您无法在本地重现 bug，您也可以获得更多信息。在我们的衍生示例中，开发人员现在可以将事实呈现给其他 IT 工程师，如网络、安全甚至服务器管理员，以进行有意义的对话来解决问题。

采用这种方法的另一个有趣的副作用是，您会发现您对系统如何连接和交互有了更广泛的理解。此外，你的“谷歌符”也得到改进，你可以键入与你的问题相关的特定短语。这是一项需要发展的优秀技能，当你发现自己处于橡皮鸭的位置时，你可以提出正确的问题来帮助其他人解决他们自己的问题。

所以去做一只橡皮鸭吧。

*由[盖丹利](https://www.flickr.com/photos/gaetanlee/)-【https://www.flickr.com/photos/gaetanlee/298160434/】， [CC 由 2.0](https://creativecommons.org/licenses/by/2.0) ，[链接](https://commons.wikimedia.org/w/index.php?curid=1456754)